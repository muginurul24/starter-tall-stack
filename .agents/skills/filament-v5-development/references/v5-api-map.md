# Filament 5 API Map

Use this as the first stop for imports and v5-specific structure. If a method or namespace is uncertain, search the official 5.x docs before writing code.

## Project Context

- `filament/filament`: 5.6.x in this project.
- Laravel: 13.x.
- Livewire: 4.x.
- Tailwind CSS: 4.x.
- Tests: Pest 4.

## Key Namespaces

| Concern | Namespace |
| --- | --- |
| Form fields | `Filament\Forms\Components` |
| Infolist entries | `Filament\Infolists\Components` |
| Layout/schema components | `Filament\Schemas\Components` |
| Schema utility injection | `Filament\Schemas\Components\Utilities` |
| Schema contracts/traits | `Filament\Schemas\Contracts`, `Filament\Schemas\Concerns` |
| Tables | `Filament\Tables` |
| Table columns | `Filament\Tables\Columns` |
| Table filters | `Filament\Tables\Filters` |
| Actions | `Filament\Actions` |
| Action testing | `Filament\Actions\Testing` |
| Notifications | `Filament\Notifications` |
| Icons | `Filament\Support\Icons\Heroicon` |
| Panels | `Filament\Panel`, `Filament\PanelProvider` |
| Resources | `Filament\Resources\Resource` |

Never use old action namespaces such as `Filament\Tables\Actions\DeleteAction` or `Filament\Forms\Actions\Action` in v5 code. Use `Filament\Actions\DeleteAction`, `Filament\Actions\Action`, etc.

## Resource Structure

Generated v5 resources commonly organize files like:

```text
app/Filament/Resources/Users/
├── UserResource.php
├── Pages/
│   ├── CreateUser.php
│   ├── EditUser.php
│   ├── ListUsers.php
│   └── ViewUser.php
├── Schemas/
│   └── UserForm.php
├── Tables/
│   └── UsersTable.php
└── RelationManagers/
```

Use generated structure already present in the project. If siblings use inline resource methods instead of `Schemas/` and `Tables/`, follow the local convention unless the change is large enough to justify extraction.

## Resource Properties

Preserve v5 property types:

```php
use BackedEnum;
use UnitEnum;

protected static string | BackedEnum | null $navigationIcon = Heroicon::OutlinedRectangleStack;

protected static string | UnitEnum | null $navigationGroup = null;
```

For Page and Widget classes, `$view` is `protected string`, not a static property.

## Forms And Schemas In Livewire Components

Custom Livewire components that render Filament schemas should:

- implement `HasSchemas`;
- use `InteractsWithSchemas`;
- define a public array property for form state, often `$data`;
- define a method like `form(Schema $schema): Schema`;
- call `$this->form->fill()` in `mount()`;
- read submitted state using `$this->form->getState()`, not raw `$this->data`.

When reachable by users and using `InteractsWithSchemas`, also consider `RestrictsFileUploadsToSchemaComponents`.

## Common Components

```php
use Filament\Forms\Components\DatePicker;
use Filament\Forms\Components\FileUpload;
use Filament\Forms\Components\Repeater;
use Filament\Forms\Components\Select;
use Filament\Forms\Components\TextInput;
use Filament\Forms\Components\Toggle;
use Filament\Infolists\Components\IconEntry;
use Filament\Infolists\Components\ImageEntry;
use Filament\Infolists\Components\TextEntry;
use Filament\Schemas\Components\Grid;
use Filament\Schemas\Components\Section;
use Filament\Tables\Columns\IconColumn;
use Filament\Tables\Columns\TextColumn;
use Filament\Tables\Filters\Filter;
use Filament\Tables\Filters\SelectFilter;
```

## Relationship Fields

Use:

```php
Select::make('author_id')
    ->relationship('author', 'name')
    ->searchable()
    ->preload()
    ->required();
```

Avoid non-existent or legacy components such as `BelongsToSelect`.

## Reactive Schema Logic

Use v5 utility injection:

```php
use Filament\Schemas\Components\Utilities\Get;
use Filament\Schemas\Components\Utilities\Set;
use Illuminate\Support\Str;

TextInput::make('title')
    ->required()
    ->live(onBlur: true)
    ->afterStateUpdated(fn (Set $set, ?string $state): null => $set('slug', Str::slug($state ?? '')));

TextInput::make('slug')
    ->required()
    ->visible(fn (Get $get): bool => filled($get('title')));
```

Prefer `->live(onBlur: true)` for derived text fields to avoid request-per-keystroke behavior.

## Tables

Use `Table` configuration with column/filter/action arrays. In v5 docs, table actions are configured with unified actions:

```php
use Filament\Actions\Action;
use Filament\Tables\Columns\TextColumn;
use Filament\Tables\Filters\SelectFilter;
use Filament\Tables\Table;

public static function configure(Table $table): Table
{
    return $table
        ->columns([
            TextColumn::make('name')
                ->searchable()
                ->sortable(),
        ])
        ->filters([
            SelectFilter::make('status')
                ->options(Status::class),
        ])
        ->recordActions([
            Action::make('archive')
                ->requiresConfirmation()
                ->action(fn (User $record): bool => $record->archive()),
        ]);
}
```

Use `headerActions()`, `recordActions()`, and `toolbarActions()` as shown in v5 docs and local generated code.

## Actions

Actions can render modal schemas:

```php
use Filament\Actions\Action;
use Filament\Forms\Components\TextInput;

Action::make('updateEmail')
    ->schema([
        TextInput::make('email')
            ->email()
            ->required(),
    ])
    ->action(fn (array $data, User $record): bool => $record->update($data));
```

Use `fillForm()` for editing existing data and `requiresConfirmation()` for destructive actions.

## Layouts

Layout components are schemas, not forms:

```php
use Filament\Forms\Components\TextInput;
use Filament\Schemas\Components\Grid;
use Filament\Schemas\Components\Section;

Section::make('Details')
    ->schema([
        Grid::make(2)
            ->schema([
                TextInput::make('first_name')->columnSpan(1),
                TextInput::make('last_name')->columnSpan(1),
                TextInput::make('bio')->columnSpanFull(),
            ]),
    ])
    ->columnSpanFull();
```

Do not assume layout components span full width by default. Add `columnSpanFull()` where needed.
