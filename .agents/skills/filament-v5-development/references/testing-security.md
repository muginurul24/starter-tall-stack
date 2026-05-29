# Filament 5 Testing And Security

Use Pest and Livewire testing helpers for panel behavior. Most resource tests should be feature tests.

## Test Setup

- Authenticate before testing panel functionality: `$this->actingAs(User::factory()->create())`.
- Use factories and useful factory states.
- For resource edit pages, instantiate the Livewire component with `['record' => $record->id]` and call `save`.
- For create pages, call `create` and expect redirect when the generated page normally redirects.
- Do not assert edit-page redirects unless the page actually redirects.

## Resource Page Tests

```php
use App\Filament\Resources\Users\Pages\CreateUser;
use App\Filament\Resources\Users\Pages\EditUser;
use App\Models\User;
use function Pest\Laravel\assertDatabaseHas;
use function Pest\Livewire\livewire;

beforeEach(function (): void {
    $this->actingAs(User::factory()->create());
});

it('can create a user', function (): void {
    livewire(CreateUser::class)
        ->fillForm([
            'name' => 'Test User',
            'email' => 'test@example.com',
        ])
        ->call('create')
        ->assertNotified()
        ->assertHasNoFormErrors()
        ->assertRedirect();

    assertDatabaseHas(User::class, [
        'name' => 'Test User',
        'email' => 'test@example.com',
    ]);
});

it('can update a user', function (): void {
    $user = User::factory()->create();

    livewire(EditUser::class, ['record' => $user->id])
        ->fillForm(['name' => 'Updated User'])
        ->call('save')
        ->assertNotified()
        ->assertHasNoFormErrors();

    assertDatabaseHas(User::class, [
        'id' => $user->id,
        'name' => 'Updated User',
    ]);
});
```

## Table Tests

```php
use App\Filament\Resources\Users\Pages\ListUsers;
use App\Models\User;
use function Pest\Livewire\livewire;

it('can search users', function (): void {
    $users = User::factory()->count(3)->create();

    livewire(ListUsers::class)
        ->assertCanSeeTableRecords($users)
        ->searchTable($users->first()->name)
        ->assertCanSeeTableRecords($users->take(1))
        ->assertCanNotSeeTableRecords($users->skip(1));
});
```

## Validation Tests

```php
livewire(CreateUser::class)
    ->fillForm([
        'name' => null,
        'email' => 'not-an-email',
    ])
    ->call('create')
    ->assertHasFormErrors([
        'name' => 'required',
        'email' => 'email',
    ])
    ->assertNotNotified();
```

## Action Tests

Use `Filament\Actions` class names or `TestAction`:

```php
use App\Filament\Resources\Users\Pages\EditUser;
use App\Models\User;
use Filament\Actions\DeleteAction;
use Filament\Actions\Testing\TestAction;
use function Pest\Livewire\livewire;

livewire(EditUser::class, ['record' => $user->id])
    ->callAction(DeleteAction::class)
    ->assertNotified();

livewire(ListUsers::class)
    ->callAction(TestAction::make('promote')->table($user), [
        'role' => 'admin',
    ])
    ->assertNotified();
```

For actions in schema components, use `schemaComponent()`. For create/edit page `getFormActions()`, target `schemaComponent('form-actions', schema: 'content')`.

## Notification Tests

Use Livewire or notification assertions:

```php
livewire(CreatePost::class)
    ->assertNotified('Post created');
```

or:

```php
use Filament\Notifications\Notification;

Notification::assertNotified();
```

## File Upload Security

`InteractsWithSchemas` composes Livewire file uploads. On reachable custom Livewire components, Livewire upload RPC methods may accept arbitrary property paths unless restricted.

Add this trait when the component should only accept uploads defined in schema upload components:

```php
use Filament\Schemas\Concerns\InteractsWithSchemas;
use Filament\Schemas\Concerns\RestrictsFileUploadsToSchemaComponents;

class CreatePost extends Component implements HasSchemas
{
    use InteractsWithSchemas;
    use RestrictsFileUploadsToSchemaComponents;
}
```

Do not assume uploaded files are public. Set explicit visibility only when public URLs are required:

```php
FileUpload::make('avatar')
    ->image()
    ->visibility('public');
```

## Import And Export Security

CSV/XLSX import/export values that begin with `=`, `+`, `-`, or `@` can be interpreted as formulas by spreadsheet tools. When values are user-supplied or untrusted:

- sanitize export values with `formatStateUsing()` or equivalent formatting;
- neutralize formulas before storing failed import rows or before users download failure CSVs;
- document expected spreadsheet risks when raw data export is intentional.

## Authorization Checklist

- Add model policies for resources and records.
- Scope resource queries when users should only see part of the data.
- Use action `visible()`, `hidden()`, `disabled()`, or authorization callbacks for UX, but keep server-side authorization definitive.
- Test unauthorized access and action denial where permission boundaries matter.

## Performance Checklist

- Eager-load relationships used by table columns, infolist entries, badges, and actions.
- Avoid expensive option queries on every render; use searchable relationship selects, preload only when bounded, or cache stable options.
- Keep computed table state display-only unless backed by explicit sorting/searching logic.
- Use pagination and filters instead of loading large record arrays into memory.
