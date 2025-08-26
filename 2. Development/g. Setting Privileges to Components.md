# g. Setting Privileges to Components

To set privileges to components we make use of a directive custom written under `app/Providers/AppServiceProvider.php`.

We set privilege with the directive `@accessview` while closing the content with `@endaccessview`

Sample usage:

```html
@accessview('faculty', 'staff')
	<button class="btn btn-primary ..."
		data-target="#sidebarPI">
		Create New
	</button>
@endaccessview
```

This directive tells the developer that only with the roles `faculty`, `staff`, and `systemadmin` are the only users capable of seeing this view. `systemadmin` roles are by default included.

> Note: that these are server-side rendered and are not just hidden using css.

To modify these roles or add roles, we modify the: 

- `app/Providers/AppServiceProviders.php`
- `app/Enums/UserRole.php`
- `app/Enum/UserPrivilege.php`

These values defined in these files above are provided by Management Information System (MIS) Database Administrator.

---

➡️ Next up!

[[h. Custom Utilities for Plugins]]