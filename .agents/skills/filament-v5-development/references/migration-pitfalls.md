# Filament 5 Migration Pitfalls

Use this when fixing older Filament code, namespace errors, broken generated resources, or model/schema/table/action API mismatches.

## Namespace Pitfalls

- Actions are unified under `Filament\Actions`. Replace old imports from `Filament\Tables\Actions` or `Filament\Forms\Actions`.
- Layout components are under `Filament\Schemas\Components`, not `Filament\Forms\Components`.
- Schema utilities such as `Get` and `Set` are under `Filament\Schemas\Components\Utilities`.
- Form inputs remain under `Filament\Forms\Components`.
- Infolist display entries remain under `Filament\Infolists\Components`.
- Icons should use `Filament\Support\Icons\Heroicon` enum values.

## API Pitfalls

- Use `->schema()` for repeaters, actions, filters, and modal forms where v5 docs define schema APIs. Do not use old `->fields()` patterns.
- Use `recordActions()` for row actions if the local v5 generated table uses it; do not reintroduce old table action namespace imports.
- Use `Select::make('author_id')->relationship('author', 'name')` for belongs-to fields.
- Do not add `->dehydrated(false)` to persisted fields. It removes the field from submitted state.
- `Grid`, `Section`, `Fieldset`, and `Repeater` do not automatically span full width. Add `->columnSpanFull()` where layout requires it.
- Use `$this->form->getState()` for standalone forms because it validates and mutates state before returning it.

## Property Type Pitfalls

Preserve v5 property types when overriding resource/page/widget properties:

```php
use BackedEnum;
use UnitEnum;

protected static string | BackedEnum | null $navigationIcon = Heroicon::OutlinedUsers;

protected static string | UnitEnum | null $navigationGroup = 'Administration';

protected string $view = 'filament.pages.settings';
```

Do not narrow these to `?string`, and do not make page/widget `$view` static.

## Testing Pitfalls

- Authenticate before panel tests.
- For edit resource tests, call `save`, not `create`.
- Do not assert redirect after edit save unless the page actually redirects.
- Use `callAction(DeleteAction::class)` for class-based prebuilt actions.
- For table actions, use `TestAction::make('name')->table($record)`.
- For schema actions, use `TestAction::make('name')->schemaComponent('component')`.

## Security Pitfalls

- `InteractsWithSchemas` exposes Livewire upload RPC behavior. Add `RestrictsFileUploadsToSchemaComponents` to reachable custom components unless broad upload paths are intentional.
- Public file URLs require explicit public visibility. Private is the safer default.
- Import/export CSV values can cause spreadsheet formula injection. Sanitize untrusted values where users download CSV/XLSX.

## Performance Pitfalls

- Relationship columns can trigger N+1 queries; eager-load or use table query configuration.
- Searchable/sortable computed state often does not work as intended. Back it with SQL expressions or explicit queries.
- Preloading large relationship select option sets can slow every form render.
- Component cache commands are for deployment; if used locally, clear cache after adding resources, pages, widgets, or relation managers.

## Docs To Check

- `https://filamentphp.com/docs/5.x/resources/overview`
- `https://filamentphp.com/docs/5.x/schemas/overview`
- `https://filamentphp.com/docs/5.x/forms/overview`
- `https://filamentphp.com/docs/5.x/tables/overview`
- `https://filamentphp.com/docs/5.x/actions/overview`
- `https://filamentphp.com/docs/5.x/testing/overview`
- `https://filamentphp.com/docs/5.x/advanced/security`
- `https://filamentphp.com/docs/5.x/deployment`
