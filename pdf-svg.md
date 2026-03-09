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
<a href="{{ route('customer.report', ['format' => 'csv']) }}" class="btn btn-success">
                            <i class="fa fa-file-csv"></i> CSV
                        </a>
                        <a href="{{ route('customer.report', ['format' => 'pdf']) }}" class="btn btn-danger">
                            <i class="fa fa-file-pdf"></i> PDF
                        </a>

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

         
