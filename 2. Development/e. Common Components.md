# e. Common Components ❗️

#important 

## Page Directives

The page already has directives setup and used throughout the application. *view `app/Providers/AppServiceProvider.php`*. The available directives are:

1. `@content` is used for defining page contents
2. `@breadcrumb` is used for defining breadcrumbs
3. `@styles` is used for defining internal css
4. `@modal` is used for defining modals within the page
5. `@sidebaroffcanvas` is used for defining sidebar off-canvas within the page
6. `@script` is used for livewire components; defines internal js
7. `@scripts` is used for non-livewire components; defines internal js

A sample `index.blade.php` structure

```html
@extends('layouts.template-admin')
@section('pagetitle', 'Personal Information')
@breadcrumb
    <x-breadcrumb>
        <li class="breadcrumb-item">Section</li>
        <li class="breadcrumb-item">Subsection</li>
        <li class="breadcrumb-item active" aria-current="page">Page</li>
    </x-breadcrumb>
@endbreadcrumb
@content
    <div class="container tw-max-w-[100rem] tw-transition-all tw-animate-scale-up" x-data="{
        showActivity: $persist(false).as('show-activity'),
        coach: $persist(true).as('coach-person')
    }">
        <div class="row gx-2">
            <div class="mb-3 tw-transition-all" 
                 x-bind:class="showActivity ? 'col-lg-10' : 'col-lg-12'">
                <x-page-header>
                    <x-slot name="heading">Heading</x-slot>
                    <x-slot name="subheading">Page Description.</x-slot>
                    <x-slot name="actions">
                        @accessview('faculty', 'staff')
                            <button class="btn btn-primary ..."
                                data-target="#sidebarPI">
                                Create New
                            </button>
                        @endaccessview
                        <button id="trailBtn" class="btn ..."
                            data-bs-toggle="tooltip" title="Toggle activity logs." 
                            data-bs-placement="bottom"
                            x-on:click="showActivity = !showActivity">
                            <i class="ri-history-line tw-text-lg"></i>
                        </button>
                    </x-slot>
                </x-page-header>
                <!-- Contents here... -->
            </div>
            <!-- Trails here... -->
            <x-activity-log route="{{ route('api.trail...') }}" />
        </div>
        <x-scroll-top />
    </div>
@endcontent
@modal
    <x-export-processing-modal />
@endmodal
@sidebaroffcanvas
    <livewire:pages.person-info.sidebar-o-c-person-info />
@endsidebaroffcanvas
@scripts
    <script>
        $(function() { ... });
    </script>
@endscripts
```

Every page within the application starts with this content. 

> Note: the page uses Alpine.js to manage state as well as manage local storage values. By defining `x-data` we persist the value to the browser thereby, remembering that state and can be used for preferences e.g., Always collapse the sidebar on-load if the user prefers it.

To learn more about this, view the docs for [Alpine JS](https://alpinejs.dev/)
#### Breadcrumbs 

Once authenticated, a page with admin view includes a breadcrumbs section for developers to set by adding this section.

```html
@breadcrumb
    <x-breadcrumb>
        <li class="breadcrumb-item">Section</li>
        <li class="breadcrumb-item">Subsection</li>
        <li class="breadcrumb-item active" aria-current="page">Page</li>
    </x-breadcrumb>
@endbreadcrumb
```

#### Sidebar Off Canvas (SOC)

A sidebar is used to manage important records within the system. This is triggered by firing events from different component. One of the common events to trigger this component is `rowSelected`.

To trigger the component on component click, make use of the property `data-target="#sidebarId"` with a class of `service-panel-toggle`

```html
<button class="btn btn-primary service-panel-toggle" data-target="#sidebarPI">
	Open Sidebar
</button>
```

The content of the sidebar is designed as such:

```html
<x-sidebar-off-canvas id="sidebarId" :open="$open">
    <div class="tw-absolute tw-left-0 tw-top-0 tw-p-5 tw-w-full tw-z-10003" x-data="{ editing: @entangle('editing') }">
        <div class="tw-sticky tw-top-0 tw-bg-neutral-50 tw-z-10003">
            <div class="tw-flex tw-gap-3 tw-items-end tw-relative">
                <x-sidebar-off-canvas-profile-image :employee="$employee" />
                <header>
                    <h1 class="tw-mb-0 tw-text-3xl tw-font-black">
                        Heading
                    </h1>
                    <p class="tw-mb-0">
                        Sidebar description goes here...
                    </p>
                </header>
            </div>
            <hr />
        </div>
        <!-- Content here... -->
    </div>
</x-sidebar-off-canvas>
@script
    <script>
        $(function () { ... });
    </script>
@endscript
```

> Note: the editing property is not required but most SOC have an editing property for CRUD operations.

To learn more about entangle properties, view the docs for [Alpine JS](https://laravel-livewire.com/docs/2.x/alpine-js)

### Modals

We define modals by using the `@modal` directive in the root page and wrapping all modal components in it.

```html
@modal
	<livewire:pages.components.modal-delete-employee />
    <x-export-processing-modal />
@endmodal
```

The content of the modal is designed as such:

```html
<x-modal id="modalId" wire:ignore.self>
    <div class="modal-header">
        <span class="tw-w-2 tw-h-2 tw-bg-teal-400 ... tw-me-2"></span>
        <h5 class="modal-title" id="modalTitle">Confirm School Deletion?</h5>
        <button type="button" 
	        class="btn-close" 
	        data-bs-dismiss="modal" 
	        aria-label="Close">
        </button>
    </div>
    <div class="modal-body">
        <!-- Content here... -->
        <div class="tw-flex tw-gap-1 tw-w-max tw-ms-auto">
            <button 
	            type="button" 
	            class="btn btn-light-danger ... waves-light tw-px-5"
                data-bs-dismiss="modal">
                Cancel
            </button>
            <button type="button" 
	            class="btn btn-danger waves-effect waves-light tw-px-5"
                data-bs-dismiss="modal">
                <i class="ri-delete-bin-7-line"></i>
                Yes, Proceed
            </button>
        </div>
    </div>
</x-modal>
```

`x-modal` is a custom written Laravel component to shorten and simplify writing modals. Available properties for this component are:

| Attribute    | Description                   | Values / Default                             |
| ------------ | ----------------------------- | -------------------------------------------- |
| `id`         | ID attribute                  | –                                            |
| `size`       | Modal size                    | `sm` \| `md` \| `lg` \| `xl` (default: `md`) |
| `position`   | Modal position                | `modal-dialog-centered`                      |
| `scrollable` | Allow modal to scroll         | `true` \| `false` (default: `false`)         |
| `static`     | Static backdrop (non-dismiss) | `true` \| `false` (default: `true`)          |

### Filter Table

Filter table are custom Livewire components built for filtering tables. This can be found all throughout forms as they are a requirement for HR to filter out fields.

#### Data-tables

Data-tables are written paired with [Datatables.net](https://datatables.net/). The jQuery plugin provides server-side rendering as well as client-side. In HRIS, we both utilize these depending on the performance needs of the application. 

For large records e.g., employee attendance, server-side processing is the go-to approach. For small records e.g., less than 1000, we use client-side.

To learn more on how to define these, view the [official documentation](https://datatables.net/examples/data_sources/server_side.html)

A sample usage is:

```js
$(function() {
	const $table = $('#personTbl');

	let table = $table.DataTable(window.baseDtConfig);
	let tableFilters = {};

	Livewire.on('filterTable', (filters) => {
		tableFilters = {...};

	$.ajax({
		url: '{{ route('api.personal.info.filter') }}',
		data: tableFilters,
		success: function(response) {
			Livewire.dispatch('filtering');

			// Destroy the existing DataTable
			if ($.fn.DataTable.isDataTable('#personTbl')) {
				table.destroy();
			}

			// Transform data to the required format
			let tableData = response.map(function(item) {
				return {...};
			});

			table = $table.DataTable({
				dom: window.dtPagination,
				pageLength: 25,
				scrollX: true,
				lengthMenu: window.dtLengthMenuShort,
				language: window.dtLanguage,
				data: tableData,
				deferRender: true,
				order: [...],
				fixedColumns: {
					leftColumns: 1,
					rightColumns: 0
				},
				columns: [...],
				createdRow: function(row, data, dataIndex) {
					window.initTooltip($table);
					window.dtClickSelectHighlightLev0(row);
				}
			});

			window.adjustTableColumns(table, 400);
		},
		error: function(jqXHR, textStatus, errorThrown) {...}
	});
});
```

For server-side rendering:

```js
const $table = $('#attendanceTbl');

let table = $table.DataTable(window.baseDtConfig);

Livewire.on('rowSelected', (rowId) => {
	// Destroy the existing DataTable
	if ($.fn.DataTable.isDataTable('#attendanceTbl')) {
		table.destroy();
	}

	table = $table.DataTable({
		processing: true,
		serverSide: true,
		scrollX: true,
		searchDelay: 350,
		pageLength: 5,
		dom: window.dtPagination,
		lengthMenu: window.dtLengthMenuShort,
		language: window.dtLanguage,
		ajax: function(data, callback, settings) {
			// Set the filter values to the Livewire component
			// to be used for filterTable func.
			@this.set('tableFilters', window.dtSetTableFilters(data));
			@this.call('filterTable');

			// This is fired from the filterTable func.
			// Update the table with the filtered data.
			Livewire.on('tableFiltered', response => {
				callback(window.dtSetResponse(response));
				window.adjustTableColumns(table);
			});
		},
		columns: [...],
		createdRow: function(row, data, dataIndex) {...},
	});
});
```

### Data-table Custom Utilities

Custom utilities are built to reduce redundancy in writing the same code.  They can be found and managed under `resources/js/dt.js`.

#### Global Utilities

These are functions for setting up data tables to reduce code.

| Utility function                              | Purpose                                                                                                                                                       |
| --------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `window.dtSetTableFilters(data)`              | Maps DataTables request payload into a backend-friendly format (`draw`, `limit`, `offset`, `order`, `search`). **Used on server-side processing**             |
| `window.dtSetResponse(response)`              | Maps backend response into a DataTables-friendly response (`draw`, `recordsTotal`, `recordsFiltered`, `data`). **Used on server-side processing**             |
| `window.adjustTableColumns(table, delay=200)` | Adjusts table column widths after rendering or resizing (wrapper for `table.columns.adjust()`).                                                               |
| `window.initTooltip(element)`                 | Initializes a custom tooltip on hover, positioned relative to its parent. **Used on `createdRow` property**                                                   |
| `window.dtClickSelectHighlightLev0(row)`      | Handles **row highlighting** for top-level tables. Clears selection when sidebar closes. **Used on `createdRow` property**                                    |
| `window.dtClickSelectHighlightLev1(row)`      | Handles **row highlighting** for tables inside a sidebar/off-canvas. Clears selection when `clearTblSelection` event fires. **Used on `createdRow` property** |
| `window.dtClickSelectHighlightLev2(row)`      | Handles **row highlighting** for nested tables inside sidebar. Clears selection when `clearTblRowSelection` event fires. **Used on `createdRow` property**    |

#### Column Utilities

These are columns used in the property `columns` in datatable. These functions can be used as such:

```js
$table.DataTable({
	columns: [
	{
		data: 'public_id',
		className: 'hover-scale',
		render: function(data, type, row) {
			return window.dataBadged(data);
		}
	},
	{
		data: 'unit_name',
		className: 'hover-scale',
		render: function(data, type, row) {
			return window.dataOrNull(data);
		}
	}]
```

| Utility function                  | Purpose |
|----------------------------------|---------|
| `window.dataOrNull(data)`         | Returns value or a styled `"Unassigned"` placeholder if empty. |
| `window.dataBadged(data)`         | Displays data inside a badge-like styled element, or `"Unassigned"` if empty. |
| `window.dataWithIcon(data)`       | Displays data alongside an icon (e.g., `<i class="..."></i> value`). |
| `window.formatDate(data)`         | Formats date into `MMM DD, YYYY` with a calendar icon. |
| `window.formatDateWithTime(data)` | Formats datetime into `MMM DD, YYYY h:mm AM/PM` with a calendar icon. |
| `window.formatTime(data)`         | Formats and displays time only (`h:mm AM/PM`) with a clock icon. |
| `window.dataSeeMore(data)`        | Truncates long text to `length` characters and provides “See more/less” toggle. |
#### Info. Card

`info-card` components are Livewire components usually found when a CRUD operation is needed. `info-card` components are usually created in separated files in order to isolate CRUD operations to a single file.

An `info-card` will often have this structure:

```html
<div x-show="mngWorkSched" x-transition>
    <div class="tw-animate-in tw-fade-in" x-data="{ editing: false }">
        <div class="row gx-2">
            <div class="mt-3 col-lg-12">
                <x-alert theme="secondary">
                    <!-- Notes/tips goes here... -->
                </x-alert>
            </div>
        </div>
        @include('modules.sys.timesys....partials.ws-manage-card')
        @include('modules.sys.timesys....partials.ws-create-card')
    </div>
</div>
@script
    <script>
        $(function() {...}
    </script>
@endscript
```

Storing both partial views for managing cards and creating records. As these switch depending on `editing` mode values.

---

➡️ Next up!

[[f. Common Livewire Events]]