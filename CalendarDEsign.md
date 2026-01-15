```bash

<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Lunch Tracker Calendar - Static Version</title>

  <!-- Bootstrap 5 CSS -->
  <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-QWTKZyjpPEjISv5WaRU9OFeRpok6YctnYmDr5pNlyT2bRjXh0JMhjY6hW+ALEwIH" crossorigin="anonymous">
  
  <!-- Bootstrap Icons -->
  <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap-icons@1.11.3/font/bootstrap-icons.min.css">

  <style>
    body {
      background: #f0f2f5;
      font-family: system-ui, -apple-system, BlinkMacSystemFont, sans-serif;
    }

    .calendar-wrapper {
      max-width: 1100px;
      margin: 2.5rem auto;
      padding: 0 15px;
    }

    .calendar {
      background: white;
      border-radius: 16px;
      overflow: hidden;
      box-shadow: 0 10px 40px rgba(0,0,0,0.1);
    }

    .calendar-header {
      background: linear-gradient(135deg, #0d6efd 0%, #6610f2 100%);
      color: white;
      padding: 2rem 1.5rem;
      text-align: center;
    }

    .month-year {
      font-size: 2.1rem;
      font-weight: 700;
      margin-bottom: 0.5rem;
    }

    .calendar-nav {
      display: flex;
      justify-content: center;
      align-items: center;
      gap: 2rem;
      margin-bottom: 1rem;
      flex-wrap: wrap;
    }

    .nav-arrow {
      width: 50px;
      height: 50px;
      border-radius: 50%;
      background: rgba(255,255,255,0.25);
      color: white;
      border: none;
      font-size: 1.5rem;
      display: flex;
      align-items: center;
      justify-content: center;
      transition: all 0.25s;
    }

    .nav-arrow:hover {
      background: rgba(255,255,255,0.45);
      transform: scale(1.1);
    }

    .calendar-table th {
      background: #e9ecef;
      color: #343a40;
      font-weight: 600;
      padding: 1rem;
      text-align: center;
      border-bottom: 2px solid #dee2e6;
    }

    .calendar-table td {
      height: 140px;
      border: 1px solid #dee2e6;
      position: relative;
      vertical-align: middle;
      padding: 10px;
      transition: background-color 0.2s;
    }

    .calendar-table td:hover {
      background-color: #f8fbff;
    }

    .date-number {
      position: absolute;
      top: 12px;
      left: 14px;
      font-size: 1.35rem;
      font-weight: 700;
      color: #495057;
    }

    .status-icon {
      position: absolute;
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%);
      font-size: 5.8rem;
      line-height: 1;
    }

    .edit-icon {
      position: absolute;
      top: 10px;
      right: 12px;
      width: 38px;
      height: 38px;
      background: rgba(255,255,255,0.9);
      border: 1px solid #dee2e6;
      border-radius: 50%;
      display: flex;
      align-items: center;
      justify-content: center;
      color: #6c757d;
      font-size: 1.1rem;
      cursor: pointer;
      transition: all 0.25s;
      box-shadow: 0 2px 8px rgba(0,0,0,0.08);
    }

    .edit-icon:hover {
      background: #0d6efd;
      color: white;
      transform: scale(1.15);
    }

    /* Status colors */
    .status-eaten    .status-icon { color: #28a745; } /* green tick */
    .status-not      .status-icon { color: #dc3545; } /* red cross */
    .status-cancel   .status-icon { color: #fd7e14; } /* orange cross */
    .status-unknown  .status-icon { color: #adb5bd; } /* grey question */

    .today {
      border: 3px solid #0d6efd !important;
      background-color: #e7f1ff !important;
    }

    .past {
      background-color: #f8f9fa;
      opacity: 0.88;
    }

    @media (max-width: 992px) {
      .calendar-table td { height: 120px; }
      .status-icon { font-size: 5rem; }
    }

    @media (max-width: 768px) {
      .month-year { font-size: 1.8rem; }
      .nav-arrow { width: 44px; height: 44px; font-size: 1.3rem; }
      .calendar-table td { height: 100px; }
      .status-icon { font-size: 4.2rem; }
      .date-number { font-size: 1.2rem; }
      .edit-icon { width: 34px; height: 34px; font-size: 1rem; }
    }

    @media (max-width: 576px) {
      .calendar-table th { font-size: 0.85rem; padding: 0.7rem; }
      .calendar-table td { height: 80px; }
      .status-icon { font-size: 3.4rem; }
    }
  </style>
</head>
<body>

<div class="calendar-wrapper">

  <div class="calendar">

    <!-- Header -->
    <div class="calendar-header">
      <div class="calendar-nav">
        <button class="nav-arrow"><i class="bi bi-chevron-left"></i></button>
        <div>
          <div class="month-year">January 2026</div>
        </div>
        <button class="nav-arrow"><i class="bi bi-chevron-right"></i></button>
      </div>
      <p class="mb-0 opacity-75">Lunch Provided Status • Dhaka</p>
    </div>

    <!-- Calendar Table -->
    <div class="table-responsive">
      <table class="calendar-table table table-borderless m-0">
        <thead>
          <tr>
            <th>Sun</th>
            <th>Mon</th>
            <th>Tue</th>
            <th>Wed</th>
            <th>Thu</th>
            <th>Fri</th>
            <th>Sat</th>
          </tr>
        </thead>
        <tbody>
          <!-- Week 1 -->
          <tr>
            <td></td><td></td><td></td><td></td>
            <td class="status-eaten today">
              <div class="date-number">1</div>
              <i class="bi bi-check-circle-fill status-icon"></i>
              <div class="edit-icon"><i class="bi bi-pencil"></i></div>
            </td>
            <td class="status-not">
              <div class="date-number">2</div>
              <i class="bi bi-x-circle-fill status-icon"></i>
              <div class="edit-icon"><i class="bi bi-pencil"></i></div>
            </td>
            <td class="status-eaten">
              <div class="date-number">3</div>
              <i class="bi bi-check-circle-fill status-icon"></i>
              <div class="edit-icon"><i class="bi bi-pencil"></i></div>
            </td>
          </tr>

          <!-- Week 2 -->
          <tr>
            <td class="status-not">
              <div class="date-number">4</div>
              <i class="bi bi-x-circle-fill status-icon"></i>
              <div class="edit-icon"><i class="bi bi-pencil"></i></div>
            </td>
            <td class="status-eaten">
              <div class="date-number">5</div>
              <i class="bi bi-check-circle-fill status-icon"></i>
              <div class="edit-icon"><i class="bi bi-pencil"></i></div>
            </td>
            <td class="status-cancel">
              <div class="date-number">6</div>
              <i class="bi bi-x-circle-fill status-icon"></i>
              <div class="edit-icon"><i class="bi bi-pencil"></i></div>
            </td>
            <td class="status-eaten">
              <div class="date-number">7</div>
              <i class="bi bi-check-circle-fill status-icon"></i>
              <div class="edit-icon"><i class="bi bi-pencil"></i></div>
            </td>
            <td class="status-not">
              <div class="date-number">8</div>
              <i class="bi bi-x-circle-fill status-icon"></i>
              <div class="edit-icon"><i class="bi bi-pencil"></i></div>
            </td>
            <td class="status-eaten">
              <div class="date-number">9</div>
              <i class="bi bi-check-circle-fill status-icon"></i>
              <div class="edit-icon"><i class="bi bi-pencil"></i></div>
            </td>
            <td class="status-eaten">
              <div class="date-number">10</div>
              <i class="bi bi-check-circle-fill status-icon"></i>
              <div class="edit-icon"><i class="bi bi-pencil"></i></div>
            </td>
          </tr>

          <!-- Week 3 -->
          <tr>
            <td class="status-not">
              <div class="date-number">4</div>
              <i class="bi bi-x-circle-fill status-icon"></i>
              <div class="edit-icon"><i class="bi bi-pencil"></i></div>
            </td>
            <td class="status-eaten">
              <div class="date-number">5</div>
              <i class="bi bi-check-circle-fill status-icon"></i>
              <div class="edit-icon"><i class="bi bi-pencil"></i></div>
            </td>
            <td class="status-cancel">
              <div class="date-number">6</div>
              <i class="bi bi-x-circle-fill status-icon"></i>
              <div class="edit-icon"><i class="bi bi-pencil"></i></div>
            </td>
            <td class="status-eaten">
              <div class="date-number">7</div>
              <i class="bi bi-check-circle-fill status-icon"></i>
              <div class="edit-icon"><i class="bi bi-pencil"></i></div>
            </td>
            <td class="status-not">
              <div class="date-number">8</div>
              <i class="bi bi-x-circle-fill status-icon"></i>
              <div class="edit-icon"><i class="bi bi-pencil"></i></div>
            </td>
            <td class="status-eaten">
              <div class="date-number">9</div>
              <i class="bi bi-check-circle-fill status-icon"></i>
              <div class="edit-icon"><i class="bi bi-pencil"></i></div>
            </td>
            <td class="status-eaten">
              <div class="date-number">10</div>
              <i class="bi bi-check-circle-fill status-icon"></i>
              <div class="edit-icon"><i class="bi bi-pencil"></i></div>
            </td>
          </tr>

          <!-- Week 4 -->
          <tr>
            <td class="status-not">
              <div class="date-number">4</div>
              <i class="bi bi-x-circle-fill status-icon"></i>
              <div class="edit-icon"><i class="bi bi-pencil"></i></div>
            </td>
            <td class="status-eaten">
              <div class="date-number">5</div>
              <i class="bi bi-check-circle-fill status-icon"></i>
              <div class="edit-icon"><i class="bi bi-pencil"></i></div>
            </td>
            <td class="status-cancel">
              <div class="date-number">6</div>
              <i class="bi bi-x-circle-fill status-icon"></i>
              <div class="edit-icon"><i class="bi bi-pencil"></i></div>
            </td>
            <td class="status-eaten">
              <div class="date-number">7</div>
              <i class="bi bi-check-circle-fill status-icon"></i>
              <div class="edit-icon"><i class="bi bi-pencil"></i></div>
            </td>
            <td class="status-not">
              <div class="date-number">8</div>
              <i class="bi bi-x-circle-fill status-icon"></i>
              <div class="edit-icon"><i class="bi bi-pencil"></i></div>
            </td>
            <td class="status-eaten">
              <div class="date-number">9</div>
              <i class="bi bi-check-circle-fill status-icon"></i>
              <div class="edit-icon"><i class="bi bi-pencil"></i></div>
            </td>
            <td class="status-eaten">
              <div class="date-number">10</div>
              <i class="bi bi-check-circle-fill status-icon"></i>
              <div class="edit-icon"><i class="bi bi-pencil"></i></div>
            </td>
          </tr>

          <!-- You can continue adding more weeks manually -->
          <!-- For now leaving remaining weeks empty for you to fill -->
          <tr><td colspan="7" class="text-center py-5 text-muted">More weeks...</td></tr>
        </tbody>
      </table>
    </div>

  </div>
</div>

</body>
</html>

```
