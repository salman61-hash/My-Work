Step 1 : Controller

```bash
 public function customersReport(Request $request)
    {
        $format = $request->input('format', 'csv');
        $customers = Customer::with('user')->get();
        $dateTitle = now()->format('d M Y');
        $stats = [
            'total' => $customers->count(),
        ];

        if ($format === 'pdf') {
            return $this->downloadCustomersPDF($customers, $dateTitle, $stats);
        } else {
            return $this->downloadCustomersCSV($customers, $dateTitle, $stats);
        }
    }
 Custom range
```bash
 public function customersReport(Request $request)
    {

        $format = $request->input('format', 'csv');
        $all = $request->input('count') === 'all';      
        $countNumber = $request->input('count_number');

        $customers = (new CustomerRepository())->getAllOrFindBySearch();

        if (!$all && is_numeric($countNumber)) {
            $customers = $customers->take((int) $countNumber);
        }

        $dateTitle = now()->format('d M Y');
        $stats = ['total' => $customers->count()];

        if ($format === 'pdf') {
            return $this->downloadCustomersPDF($customers, $dateTitle, $stats);
        } else {
            return $this->downloadCustomersCSV($customers, $dateTitle, $stats);
        }
    }
    ```

    private function downloadCustomersPDF($customers, $dateTitle, $stats)
    {
        $pdf = Pdf::loadView('customers.customers-pdf', [
            'customers' => $customers,
            'dateTitle' => $dateTitle,
            'stats' => $stats,
            'generatedAt' => now()->format('d M Y, h:i A')
        ]);
        $fileName = 'customers-report-' . now()->format('Y-m-d-His') . '.pdf';
        return $pdf->download($fileName);
    }

    private function downloadCustomersCSV($customers, $dateTitle, $stats)
    {
        $fileName = 'customers-report-' . now()->format('Y-m-d-His') . '.csv';
        $headers = [
            'Content-Type' => 'text/csv',
            'Content-Disposition' => "attachment; filename=\"$fileName\"",
        ];

        $callback = function () use ($customers, $dateTitle, $stats) {
            $file = fopen('php://output', 'w');

            // Title and stats
            fputcsv($file, ['Customers Report - ' . $dateTitle]);
            fputcsv($file, ['Total Customers', $stats['total']]);
            fputcsv($file, []);

            // Column headers
            fputcsv($file, ['ID', 'Name', 'Email', 'Mobile']);

            // Data rows
            foreach ($customers as $customer) {
                fputcsv($file, [
                    $customer->id,
                    $customer->user->name ?? 'N/A',
                    $customer->user->email ?? 'N/A',
                    $customer->user->mobile ?? 'N/A',
                ]);
            }

            fclose($file);
        };

        return response()->stream($callback, 200, $headers);
    }

```

step 2 : Route

```bash

Route::get('customers/report', 'customersReport')
         ->name('customer.report');
```
step 3 : blade
```bash
                        <div class="dropdown">
                            <button class="btn btn-warning dropdown-toggle" type="button" id="exportDropdown"
                                data-bs-toggle="dropdown" aria-expanded="false">
                                <i class="fa fa-file-export"></i> {{ __('Export Report') }}
                            </button>
                            <ul class="dropdown-menu" aria-labelledby="exportDropdown">
                                <li><a class="dropdown-item" href="{{ route('customer.report', ['format' => 'csv']) }}">
                                        <i class="fa fa-file-csv"></i> CSV</a></li>
                                <li><a class="dropdown-item" href="{{ route('customer.report', ['format' => 'pdf']) }}">
                                        <i class="fa fa-file-pdf"></i> PDF</a></li>
                            </ul>
                        </div>

Custom RAnge
```bash
<!-- Export Report Button -->
                        <button type="button" class="btn btn-warning" data-bs-toggle="modal" data-bs-target="#exportModal">
                            <i class="fa fa-file-export"></i> Export Report
                        </button>

                        <!-- Modal -->
                        <div class="modal fade" id="exportModal" tabindex="-1" aria-labelledby="exportModalLabel"
                            aria-hidden="true">
                            <div class="modal-dialog">
                                <div class="modal-content">
                                    <div class="modal-header">
                                        <h5 class="modal-title">Export Customers</h5>
                                        <button type="button" class="btn-close" data-bs-dismiss="modal"><i
                                                class="fas fa-times"></i></button>
                                    </div>
                                    <div class="modal-body">
                                        <!-- Format -->
                                        <div class="mb-3">
                                            <label class="form-label">Export Format</label>
                                            <select id="exportFormat" class="form-select">
                                                <option value="csv">CSV</option>
                                                <option value="pdf">PDF</option>
                                            </select>
                                        </div>

                                        <!-- All checkbox -->
                                        <div class="form-check mb-2">
                                            <input class="form-check-input" type="checkbox" id="exportAll">
                                            <label class="form-check-label" for="exportAll">All Customers</label>
                                        </div>

                                        <!-- Number input -->
                                        <input type="number" id="exportCount" class="form-control" min="1"
                                            placeholder="Enter number of customers">
                                    </div>
                                    <div class="modal-footer">
                                        <button type="button" class="btn btn-secondary"
                                            data-bs-dismiss="modal">Cancel</button>
                                        <button type="button" class="btn btn-success" id="exportSubmit">Export</button>
                                    </div>
                                </div>
                            </div>
                        </div>
    ```

```
step 4 : LoadView
```bash
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>Customers Report</title>
    <style>
        body { font-family: DejaVu Sans, sans-serif; }
        table { width: 100%; border-collapse: collapse; margin-top: 20px; }
        th, td { border: 1px solid #000; padding: 8px; text-align: left; }
        th { background-color: #f2f2f2; }
        h2, h4 { margin: 0; padding: 0; }
    </style>
</head>
<body>
    <h2>Customers Report</h2>
    <h4>Date: {{ $dateTitle }}</h4>
    <h4>Generated At: {{ $generatedAt }}</h4>
    <h4>Total Customers: {{ $stats['total'] }}</h4>

    <table>
        <thead>
            <tr>
                <th>ID</th>
                <th>Name</th>
                <th>Email</th>
                <th>Mobile</th>
            </tr>
        </thead>
        <tbody>
            @foreach ($customers as $customer)
                <tr>
                    <td>{{ $customer->id }}</td>
                    <td>{{ $customer->user->name ?? 'N/A' }}</td>
                    <td>{{ $customer->user->email ?? 'N/A' }}</td>
                    <td>{{ $customer->user->mobile ?? 'N/A' }}</td>
                </tr>
            @endforeach
        </tbody>
    </table>
</body>
</html>

         
