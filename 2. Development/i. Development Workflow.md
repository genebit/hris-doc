# i. Development Workflow

Now that you're now acquainted with the technologies, functions, and components within the application, we can now combine all of these to create a CRUD operation within the application.

In this demonstration we will create a simple CRUD operation for managing job titles for employees.

### 1. Create a Route

```php
Route::get('/mgt/jobs', [JobController::class, 'index'])->name('job');
```

### 2. Create Table

`app/Livewire/Pages/Job`

```php
<?php

namespace App\Livewire\Pages\Job;

use App\Contracts\IBaseRepository;
use Livewire\Component;

class Table extends Component
{
    public $jobs;
    private $baseRepository;

    public function boot()
    {
        $this->baseRepository = app(IBaseRepository::class);
    }

    public function getJobs()
    {
        $this->jobs = $this->baseRepository->getAllJobTitle();

        $this->dispatch('fetchedJobs');
    }

    public function render()
    {
        return view('modules.hr.job.table');
    }
}
```

`resources/modules/hr/job/table.blade.php`

```html
<div wire:init="getJobs">
    <header class="mb-3">
        <h2 class="tw-text-base tw-tracking-wider tw-font-black tw-uppercase tw-mb-0 tw-flex tw-items-center">
            <span class="tw-w-2 tw-h-2 tw-bg-teal-500 tw-rounded-full tw-inline-block tw-me-2"></span>
            Job Titles
        </h2>
    </header>
    <div class="tw-relative">
        <x-alert theme="secondary">
            Select a job title to view more details.
        </x-alert>
        <div tooltip-parent class="tw-z-10 tw-w-full" wire:ignore>
            <table id="jobsTbl" class="table table-sm tw-w-full">
                <thead>
                    <tr class="tw-border-b-2">
                        <th class="tw-whitespace-nowrap tw-transition-[width]">Title</th>
                    </tr>
                </thead>
                <tbody>
                    <x-row-skeleton rowNumber="5" colNumber="1" />
                </tbody>
            </table>
            <x-tooltip message="Select to view details" />
        </div>
    </div>
</div>
@script
    <script>
        $(function() {
            const $table = $('#jobsTbl');
            let table = $table.DataTable(window.baseDtConfig);

            Livewire.on('fetchedJobs', () => {
                // Destroy the existing DataTable
                if ($.fn.DataTable.isDataTable('#jobsTbl')) {
                    table.destroy();
                }

                let tableData = @this.jobs.map(function(item) {
                    return {
                        job_id: item.job_id,
                        job_title: item.job_title
                    };
                });

                table = $table.DataTable({
                    dom: window.dtPagination,
                    pageLength: 25,
                    scrollX: true,
                    lengthMenu: window.dtLengthMenuShort,
                    language: window.dtLanguage,
                    data: tableData,
                    order: [],
                    columns: [{
                        data: 'job_title',
                        className: 'hover-scale'
                    }],
                    createdRow: function(row, data, dataIndex) {
                        window.initTooltip($table);
                        window.dtClickSelectHighlightLev0(row);
                    },
                });

                window.adjustTableColumns(table);

                // On row click, emit the event to fetch the assignment information
                $table.on('click', 'tr', function() {
                    const data = table.row(this).data();

                    @this.dispatch('rowSelected', {
                        id: data.job_id,
                    });
                });
            });
        });
    </script>
@endscript

```

### 3. Create a Sidebar Off Canvas (SOC)

On `rowSelected`, an event will trigger for SOC to open.

`app/Livewire/Pages/Job`

```php
<?php

namespace App\Livewire\Pages\Job;

use App\Contracts\IBaseRepository;
use App\Helpers\CRUD;
use App\Models\Job;
use Illuminate\Support\Facades\Session;
use Livewire\Component;

class SidebarOCJob extends Component
{
    public $job;
    public $jobId,
        $jobTitle;

    public $open = false, $editing = true;

    // Used for dropdowns
    private $baseRepository, $jobInstance;

    protected $listeners = [
        'rowSelected' => 'getJob',
        'deleteJob',
        'close'
    ];

    protected $rules = [
        'jobId'     => 'nullable|numeric|digits_between:1,4',
        'jobTitle'  => 'required|max:300',
    ];

    public function boot()
    {
        $this->baseRepository = app(IBaseRepository::class);
        $this->jobInstance = app(Job::class);
    }

    public function updated()
    {
        $this->resetErrorBag();
    }

    public function getJob($id)
    {
        $this->open = true;

        $this->job = $this->baseRepository->getJobTitle($id);

        if ($this->job) {
            $this->jobId = $this->job->job_id;
            $this->jobTitle = $this->job->job_title;

            $this->editing = false;
        } else {
            $this->editing = true;
        }

        $this->dispatch('fetchedJob', $this->job);
    }

    public function createJob()
    {
        CRUD::handle([
            'component' => $this,
            'operate' => function () {
                $this->validate();

                // The operation logic to create a new job
                return $this->jobInstance->store(new Job([
                    'job_id'        => null,
                    'job_title'     => $this->jobTitle
                ]));
            },
            'onSuccess' => function ($result) {
                Session::flash('success', "Job: {$this->jobTitle} has been created successfully!");
                return redirect(request()->header('Referer'));
            },
            'onError' => function ($error) {
                $this->open = true;
                $this->dispatch('errorProcJob', $error);
            }
        ]);
    }

    public function updateJob()
    {
        CRUD::handle([
            'component' => $this,
            'operate' => function () {
                $this->validate();

                // The operation logic to create a new job
                return $this->jobInstance->modify(new Job([
                    'job_id'        => $this->jobId,
                    'job_title'     => $this->jobTitle
                ]));
            },
            'onSuccess' => function ($result) {
                Session::flash('success', "Job: {$this->jobTitle} has been updated successfully!");
                return redirect(request()->header('Referer'));
            },
            'onError' => function ($error) {
                $this->open = true;
                $this->dispatch('errorProcJob', $error);
            }
        ]);
    }

    public function deleteJob()
    {
        CRUD::handle([
            'component' => $this,
            'operate' => function () {
                $this->validate();

                // The operation logic to create a new job
                return $this->jobInstance->purge($this->jobId);
            },
            'onSuccess' => function ($result) {
                Session::flash('success', "Job: {$this->jobTitle} has been deleted successfully!");
                return redirect(request()->header('Referer'));
            },
            'onError' => function ($error) {
                $this->open = true;
                $this->dispatch('errorProcJob', $error);
            }
        ]);
    }

    public function clear()
    {
        $this->reset([
            'editing',
            'job',
            'jobId',
            'jobTitle'
        ]);

        $this->dispatch('clearedJob');
    }

    public function close()
    {
        $this->open = false;
        $this->clear();

        $this->dispatch('closedSidebar');
    }

    public function render()
    {
        return view('modules.hr.job.sidebar-oc-content');
    }
}

```

`resources/modules/hr/job/sidebar-oc-content.blade.php`

```html
<x-sidebar-off-canvas id="sidebarJob" :open="$open">
    <div class="tw-absolute tw-left-0 tw-top-0 tw-p-5 tw-w-full">
        <div class="tw-sticky tw-top-0 tw-bg-neutral-50 tw-z-10003">
            <header class="tw-flex tw-justify-between">
                <section>
                    @if (collect($job)->isEmpty())
                        <h1 class="tw-mb-0 tw-text-3xl tw-font-black">Create Job</h1>
                        <p class="tw-mb-0">This will insert a new office record within the University.</p>
                    @else
                        <h1 class="tw-mb-0 tw-text-3xl tw-font-black">Update Job</h1>
                        <p class="tw-mb-0">Update the necessary information of the selected office
                            <b>{{ $jobTitle }}</b>.
                        </p>
                    @endif
                </section>
            </header>
            <hr />
        </div>
        <form x-data="{ editing: @entangle('editing') }" wire:submit.prevent="{{ collect($job)->isNotEmpty() ? 'updateJob' : 'createJob' }}">
            <div class="card">
                <div class="border card-body border-1 tw-rounded-md">
                    <header class="tw-flex tw-justify-between">
                        <div class="tw-w-max">
                            <h2
                                class="tw-text-base tw-tracking-wider tw-font-black tw-uppercase tw-mb-0 tw-flex tw-items-center">
                                <span
                                    class="tw-w-2 tw-h-2 tw-bg-teal-500 tw-rounded-full tw-inline-block tw-me-2"></span>
                                Job Information
                            </h2>
                            <p>Fill in the necessary information to create a new job.</p>
                        </div>
                        @accessview('attmonitorstaff', 'issofficer', 'recordsmgtspec')
                            <div class="tw-ms-auto tw-w-max">
                                @if (collect($job)->isNotEmpty())
                                    <button type="button" class="btn text-danger waves-effect waves-danger tw-px-5"
                                        data-bs-toggle="modal" data-bs-target="#confirmMDeleteJB"
                                        x-on:click="$dispatch('confirmMDeleteJB')" x-show="editing" x-transition>
                                        <i class="ri-delete-bin-7-line"></i>
                                        Delete
                                    </button>
                                @endif
                                <button type="submit" class="btn btn-primary tw-px-5 waves-effect waves-light"
                                    wire:loading.attr="disabled" x-bind:disabled="!editing"
                                    wire:target="createJob, updateJob">{{ collect($job)->isNotEmpty() ? 'Update' : 'Create' }}
                                    Job
                                </button>
                            </div>
                        @endaccessview
                    </header>
                    @accessview('attmonitorstaff', 'issofficer', 'recordsmgtspec')
                        <div class="row gx-2">
                            <div class="mb-3 col-lg-12">
                                <div class="form-check form-switch tw-w-max">
                                    <label class="form-check-label" for="jobEditingModeCB">
                                        Editing Mode
                                    </label>
                                    <input class="border form-check-input border-1 tw-border-slate-400" type="checkbox"
                                        role="switch" id="jobEditingModeCB" x-on:click="editing = !editing"
                                        x-bind:checked="editing">
                                </div>
                            </div>
                        </div>
                    @endaccessview
                    <div class="row gx-2">
                        <div class="mb-3 col-lg-12">
                            <div class="form-floating">
                                <input type="text" autocomplete="off"
                                    class="form-control @error('jobTitle') is-invalid @enderror" id="jobTitle"
                                    wire:model.live="jobTitle" x-bind:disabled="!editing">
                                <label for="jobTitle">Job Title
                                    <x-indicator />
                                </label>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </form>
    </div>
</x-sidebar-off-canvas>
```

> Since this just a simple sidebar with no datatables within it, there's no need for an `info-card` component.

### 4. Create a Modal for Confirming Deletion

```php
<?php

namespace App\Livewire\Pages\Job;

use Livewire\Component;

class ModalDeleteJob extends Component
{
    public function confirmDeletion()
    {
        $this->dispatch('deleteJob');
    }

    public function render()
    {
        return view('modules.hr.job.modal-delete-jb');
    }
}

```

`resources/modules/hr/job/modal-delete-jb.blade.php`

```html
<x-modal id="confirmMDeleteJB" wire:ignore.self>
    <div class="modal-header">
        <span class="tw-w-2 tw-h-2 tw-bg-red-300 tw-inline-block rounded-circle tw-me-2"></span>
        <h5 class="modal-title" id="modalTitle">Confirm Job Deletion?</h5>
        <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
    </div>
    <div class="modal-body">
        <p>
            Are you sure you want to delete this job title? This action cannot be undone.
        </p>
        <div class="tw-flex tw-gap-1 tw-w-max tw-ms-auto">
            <button type="button" class="btn btn-light-danger text-danger waves-effect waves-light tw-px-5"
                data-bs-dismiss="modal">
                Cancel
            </button>
            <button type="button" class="btn btn-danger waves-effect waves-light tw-px-5" wire:click="confirmDeletion"
                wire:loading.attr="disabled" data-bs-dismiss="modal">
                <i class="ri-delete-bin-7-line"></i>
                Yes, Proceed
            </button>
        </div>
    </div>
</x-modal>
```

---

➡️ Next up!

[[3. Deployment/a. Prerequisites|Deployment!]]