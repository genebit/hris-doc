# 📍 f. Common Livewire Events

Since Livewire v3 is used all throughout the codebase, different kinds of events are dispatched for every operations. 

Among these operations include common events that are just reused from module to module.

### Common Events

| Event                 | Purpose                                                  | Notes / Examples                                 |
| --------------------- | -------------------------------------------------------- | ------------------------------------------------ |
| `rowSelected`         | Notifies components that a table row has been selected   | –                                                |
| `populatedDropdown`   | Notifies components that dropdown(s) are now populated   | –                                                |
| `populateDropdown`    | Populates a dropdown                                     | –                                                |
| `setFilters`          | Sets filter values for tables                            | –                                                |
| `filtering`           | Notifies components that a filtering action is happening | –                                                |
| `tableFiltered`       | Notifies that a table has finished filtering             | –                                                |
| `refreshTable`        | Refreshes the table records                              | –                                                |
| `successProc{module}` | Successful CRUD/procedure; suffixed with the module name | e.g., `successProcEmployee`, `successProcPerson` |
| `errorProc{module}`   | Failed CRUD/procedure; suffixed with the module name     | e.g., `errorProcEmployee`, `errorProcPerson`     |
| `successExport`       | Successful data export                                   | –                                                |
| `errorExport`         | Failed data export                                       | –                                                |
| `closedSidebar`       | Sidebar is closed (notifies listeners)                   | –                                                |
| `closedModal`         | Modal is closed                                          | –                                                |
| `clearSelected`       | Clears the current table selection                       | –                                                |
| `clear`               | Clears a form                                            | –                                                |
| `close`               | Closes a sidebar                                         | –                                                |
| `delete{module}`      | Dispatches delete actions; suffixed with the module name | e.g., `deleteWorkSchedule`, `deleteJob`          |

---

➡️ Next up!

[[g. Setting Privileges to Components]]