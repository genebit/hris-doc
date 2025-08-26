# d. Common Components

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

```bash
@extends('layouts.template-admin')
@section('pagetitle', 'Personal Information')
@breadcrumb
    <x-breadcrumb>
        <li class="breadcrumb-item">CORE</li>
        <li class="breadcrumb-item">Management</li>
        <li class="breadcrumb-item active" aria-current="page">Personal Information</li>
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
                    <x-slot name="heading">Personal Information</x-slot>
                    <x-slot name="subheading">Manage employee personal information.</x-slot>
                    <x-slot name="actions">
                        @accessview('attmonitorstaff', 'recordsmgtspec', 'issofficer')
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
                @include('modules.hr.personal-info.components.kpis')
                <livewire:pages.person-info.filter-table />
                <livewire:pages.person-info.person-table />
            </div>
            <x-activity-log route="{{ route('api.trail.prsn') }}" />
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
        $(function() {
            // ---------------------------------------------------
            // C.R.U.D. Personal Info Tab
            Livewire.on('successProcEmployee', (data) => window.successNotyf(data[0].message));
            Livewire.on('errorProcEmployee', (message) => window.errorNotyf(message));
            // ...
        });
    </script>
@endscripts
```

#### Sidebar Off Canvas (SOC)

#### Breadcrumbs 

### Modals

## Forms

### Filter Table

#### Datatables

#### Info. Card

