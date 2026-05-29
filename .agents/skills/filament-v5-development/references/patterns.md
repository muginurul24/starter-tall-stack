# Filament 5 Patterns

Use these patterns to build idiomatic Filament 5 panels and components. Keep following local project conventions when they are already consistent with v5.

## Artisan First

Use Filament generators for new files:

```bash
php artisan make:filament-resource User --no-interaction
php artisan make:filament-page Settings --no-interaction
php artisan make:filament-widget StatsOverview --no-interaction
php artisan make:filament-relation-manager UserResource posts title --no-interaction
```

Command names and options can vary. Check with:

```bash
php artisan list | rg filament
php artisan make:filament-resource --help
```

Use Laravel generators for models, policies, migrations, factories, and tests where appropriate.

## Resource Build Checklist

1. Confirm the model, migration columns, relationships, casts, enums, policy, and factory.
2. Generate the resource with Filament artisan commands when possible.
3. Put form fields in the resource schema or generated `Schemas/*Form.php` class.
4. Put table columns, filters, actions, and bulk actions in the resource table or generated `Tables/*Table.php` class.
5. Add relation managers only for relationships that users need to manage inline.
6. Add policies and record query scoping; do not rely on hidden buttons as authorization.
7. Add focused Pest tests for page loading, create/edit/save validation, table rendering/search/filtering, and important actions.

## Form Schema Pattern

```php
use Filament\Forms\Components\Select;
use Filament\Forms\Components\TextInput;
use Filament\Schemas\Components\Section;
use Filament\Schemas\Components\Utilities\Get;
use Filament\Schemas\Components\Utilities\Set;
use Filament\Schemas\Schema;
use Illuminate\Support\Str;

public static function configure(Schema $schema): Schema
{
    return $schema
        ->components([
            Section::make('Details')
                ->schema([
                    TextInput::make('title')
                        ->required()
                        ->maxLength(255)
                        ->live(onBlur: true)
                        ->afterStateUpdated(fn (Set $set, ?string $state): mixed => $set('slug', Str::slug($state ?? ''))),

                    TextInput::make('slug')
                        ->required()
                        ->maxLength(255)
                        ->unique(ignoreRecord: true),

                    Select::make('author_id')
                        ->relationship('author', 'name')
                        ->searchable()
                        ->preload()
                        ->required()
                        ->visible(fn (Get $get): bool => filled($get('title'))),
                ])
                ->columns(2)
                ->columnSpanFull(),
        ]);
}
```

Use `->statePath('data')` and `$this->form->getState()` for standalone Livewire schemas.

## Repeater Pattern

Use `Repeater::make()->schema()`, not `fields()`:

```php
use Filament\Forms\Components\Repeater;
use Filament\Forms\Components\TextInput;

Repeater::make('qualifications')
    ->relationship()
    ->schema([
        TextInput::make('institution')
            ->required(),
        TextInput::make('qualification')
            ->required(),
    ])
    ->columns(2);
```

For JSON repeater data, use a JSON column and an Eloquent `array` cast.

## Table Pattern

```php
use Filament\Actions\Action;
use Filament\Actions\DeleteAction;
use Filament\Tables\Columns\IconColumn;
use Filament\Tables\Columns\TextColumn;
use Filament\Tables\Filters\Filter;
use Filament\Tables\Filters\SelectFilter;
use Filament\Tables\Table;
use Illuminate\Database\Eloquent\Builder;

public static function configure(Table $table): Table
{
    return $table
        ->columns([
            TextColumn::make('title')
                ->searchable()
                ->sortable(),
            TextColumn::make('author.name')
                ->label('Author')
                ->sortable(),
            IconColumn::make('is_published')
                ->boolean(),
        ])
        ->filters([
            SelectFilter::make('author')
                ->relationship('author', 'name'),
            Filter::make('published')
                ->query(fn (Builder $query): Builder => $query->whereNotNull('published_at')),
        ])
        ->recordActions([
            Action::make('publish')
                ->requiresConfirmation()
                ->visible(fn (Post $record): bool => blank($record->published_at))
                ->action(fn (Post $record): bool => $record->update(['published_at' => now()])),
            DeleteAction::make(),
        ]);
}
```

When using computed table values, prefer `state(fn (Model $record): string => ...)` for display-only columns. If the value must be sortable/searchable, implement query logic instead of relying on PHP-only state.

## Action Pattern

```php
use Filament\Actions\Action;
use Filament\Forms\Components\Textarea;
use Filament\Notifications\Notification;

Action::make('reject')
    ->color('danger')
    ->requiresConfirmation()
    ->schema([
        Textarea::make('reason')
            ->required()
            ->maxLength(1000),
    ])
    ->action(function (array $data, Application $record): void {
        $record->reject($data['reason']);

        Notification::make()
            ->title('Application rejected')
            ->success()
            ->send();
    });
```

For reusable actions, create a small class with `public static function make(): Action`. For action testing by class name, use `#[ActionName('name')]` or extend `Action` and implement `getDefaultName()`.

## Panel Configuration

Panel providers configure path, auth, resources, pages, widgets, middleware, render hooks, notifications, plugins, colors, and branding. Keep panel-specific changes in the panel provider unless the feature belongs globally.

Useful v5 features:

- `->databaseNotifications()` to enable panel database notifications.
- `->renderHook()` for panel-specific Blade insertion points.
- `->plugin()` for plugin registration.
- `->resources([...])` can register regular resource classes and configured resources.

## Configurable Resources

Filament 5 supports configured resource registrations. A resource can define a `$configurationClass`, then be registered more than once:

```php
$panel->resources([
    OrderResource::class,
    OrderResource::make('active')
        ->navigationLabel('Active Orders'),
    OrderResource::make('archived')
        ->navigationLabel('Archived Orders')
        ->archived(),
]);
```

Use `static::getConfiguration()` or `static::hasConfiguration()` inside the resource to alter queries, labels, navigation groups, or page behavior. Use `getUrl(configuration: 'archived')` to link to a specific configured registration.

## Assets And Rich Editor Extensions

Register custom JS/CSS assets through Filament asset APIs, not ad hoc script tags. For rich editor TipTap extensions, use `FilamentAsset::register()` and `loadedOnRequest()` so bundles are loaded only when needed. Avoid bundling duplicate TipTap/ProseMirror copies when integrating with Filament's bundled editor modules.

## Deployment

In production deployment scripts, run:

```bash
php artisan filament:optimize
```

This caches Filament components and Blade icons. Clear with:

```bash
php artisan filament:optimize-clear
```

Avoid component cache commands during active local development unless you know to clear/rebuild the cache after creating components.
