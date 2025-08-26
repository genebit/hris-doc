# e. Common Components

## Page

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

#### Datatables

#### Info. Card

---

➡️ Next up!

[[f. Common Livewire Events]]