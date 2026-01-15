```bash
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Lunch Tracker Calendar - Responsive</title>

  <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" rel="stylesheet" />
  <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap-icons@1.11.3/font/bootstrap-icons.min.css" />

  <style>
    body {
      background: #f0f2f5;
      min-height: 100vh;
      font-family: system-ui, -apple-system, sans-serif;
    }

    .calendar-container {
      max-width: 1100px;
      margin: 2rem auto;
      padding: 0 1rem;
    }

    .calendar {
      background: white;
      border-radius: 1.2rem;
      overflow: hidden;
      box-shadow: 0 10px 35px rgba(0,0,0,0.12);
    }

    .calendar-header {
      background: linear-gradient(135deg, #0d6efd 0%, #6610f2 100%);
      color: white;
      padding: 1.8rem 1.5rem;
      text-align: center;
    }

    .month-year-nav {
      display: flex;
      align-items: center;
      justify-content: center;
      gap: 1.5rem;
      font-size: 1.6rem;
      font-weight: 700;
    }

    .nav-btn {
      background: rgba(255,255,255,0.25);
      border: none;
      color: white;
      width: 48px;
      height: 48px;
      border-radius: 50%;
      display: flex;
      align-items: center;
      justify-content: center;
      cursor: pointer;
      transition: all 0.25s;
      font-size: 1.4rem;
    }

    .nav-btn:hover {
      background: rgba(255,255,255,0.4);
      transform: scale(1.1);
    }

    .month-year-display {
      min-width: 220px;
      text-align: center;
    }

    .calendar-body table {
      width: 100%;
      border-collapse: separate;
      border-spacing: 0;
    }

    .calendar-body th {
      background: #e9ecef;
      padding: 1rem;
      text-align: center;
      font-weight: 600;
      color: #495057;
      border-bottom: 2px solid #dee2e6;
    }

    .calendar-body td {
      border: 1px solid #dee2e6;
      height: 130px;
      position: relative;
      padding: 0.8rem;
      vertical-align: middle;
      transition: background 0.2s;
    }

    .calendar-body td:hover {
      background: #f8fbff;
    }

    .date-num {
      position: absolute;
      top: 10px;
      left: 12px;
      font-size: 1.25rem;
      font-weight: 700;
      color: #495057;
      z-index: 5;
    }

    .status-icon {
      position: absolute;
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%);
      font-size: 5.5rem;
      line-height: 1;
    }

    .edit-btn {
      position: absolute;
      top: 8px;
      right: 10px;
      background: rgba(255,255,255,0.9);
      border: 1px solid #dee2e6;
      border-radius: 50%;
      width: 38px;
      height: 38px;
      display: flex;
      align-items: center;
      justify-content: center;
      color: #6c757d;
      font-size: 1.1rem;
      cursor: pointer;
      transition: all 0.25s;
      box-shadow: 0 2px 6px rgba(0,0,0,0.1);
      z-index: 10;
    }

    .edit-btn:hover {
      background: #0d6efd;
      color: white;
      transform: scale(1.12);
    }

    .today {
      border: 3px solid #0d6efd !important;
      background: #e7f1ff !important;
    }

    .past {
      background: #f8f9fa;
      opacity: 0.9;
    }

    .future .status-icon {
      opacity: 0.45;
    }

    /* Status colors */
    .eaten    .status-icon { color: #28a745; }
    .not-eaten .status-icon { color: #dc3545; }
    .cancelled .status-icon { color: #fd7e14; }
    .unknown  .status-icon { color: #6c757d; }

    @media (max-width: 768px) {
      .month-year-nav {
        font-size: 1.35rem;
        gap: 1rem;
      }
      .nav-btn {
        width: 40px;
        height: 40px;
        font-size: 1.2rem;
      }
      .calendar-body td {
        height: 100px;
      }
      .status-icon {
        font-size: 4.2rem;
      }
      .date-num {
        font-size: 1.1rem;
      }
      .edit-btn {
        width: 32px;
        height: 32px;
        font-size: 0.95rem;
      }
    }

    @media (max-width: 576px) {
      .month-year-display {
        font-size: 1.2rem;
        min-width: auto;
      }
      .calendar-body th {
        padding: 0.7rem;
        font-size: 0.85rem;
      }
      .calendar-body td {
        height: 80px;
      }
      .status-icon {
        font-size: 3.4rem;
      }
    }
  </style>
</head>
<body>

<div class="calendar-container">

  <div class="calendar">

    <!-- Header with navigation -->
    <div class="calendar-header">
      <div class="month-year-nav">
        <button class="nav-btn" id="prevMonth"><i class="bi bi-chevron-left"></i></button>
        
        <div class="month-year-display">
          <span id="currentMonth">January</span> 
          <span id="currentYear">2026</span>
        </div>
        
        <button class="nav-btn" id="nextMonth"><i class="bi bi-chevron-right"></i></button>
      </div>
      
      <p class="mb-0 mt-2 opacity-75">
        Track your daily lunch • Green ✓ = Eaten • Red ✗ = Not Eaten
      </p>
    </div>

    <!-- Calendar Grid -->
    <div class="calendar-body">
      <table>
        <thead>
          <tr>
            <th>Sun</th><th>Mon</th><th>Tue</th><th>Wed</th>
            <th>Thu</th><th>Fri</th><th>Sat</th>
          </tr>
        </thead>
        <tbody id="calendarBody">
          <!-- Days will be generated by JavaScript -->
        </tbody>
      </table>
    </div>

  </div>
</div>

<script>
  // Simple calendar logic (demo purpose - you can extend it)
  const months = [
    "January","February","March","April","May","June",
    "July","August","September","October","November","December"
  ];

  let currentDate = new Date(2026, 0, 15); // January 15, 2026

  function renderCalendar() {
    const year = currentDate.getFullYear();
    const month = currentDate.getMonth();
    
    document.getElementById('currentMonth').textContent = months[month];
    document.getElementById('currentYear').textContent = year;

    const firstDay = new Date(year, month, 1);
    const lastDay = new Date(year, month + 1, 0);
    const daysInMonth = lastDay.getDate();
    const startingDay = firstDay.getDay(); // 0 = Sunday

    let html = '';
    let day = 1;

    // First row
    html += '<tr>';
    for(let i = 0; i < 7; i++) {
      if(i < startingDay) {
        html += '<td></td>';
      } else if(day <= daysInMonth) {
        html += createDayCell(day, month, year);
        day++;
      }
    }
    html += '</tr>';

    // Remaining rows
    while(day <= daysInMonth) {
      html += '<tr>';
      for(let i = 0; i < 7 && day <= daysInMonth; i++) {
        html += createDayCell(day, month, year);
        day++;
      }
      html += '</tr>';
    }

    document.getElementById('calendarBody').innerHTML = html;
  }

  function createDayCell(day, month, year) {
    const today = new Date();
    const isToday = (day === today.getDate() && 
                     month === today.getMonth() && 
                     year === today.getFullYear());

    // You can change status logic here (demo values)
    let statusClass = 'unknown';
    let icon = 'bi-question-circle-fill';
    
    // Example random/demo status
    if(day % 5 === 0) statusClass = 'eaten', icon = 'bi-check-circle-fill';
    else if(day % 7 === 0) statusClass = 'not-eaten', icon = 'bi-x-circle-fill';
    else if(day % 11 === 0) statusClass = 'cancelled', icon = 'bi-x-circle-fill';

    if(isToday) statusClass += ' today';

    return `
      <td class="${statusClass}">
        <div class="date-num">${day}</div>
        <i class="status-icon bi ${icon}"></i>
        <button class="edit-btn" title="Change status">
          <i class="bi bi-pencil"></i>
        </button>
      </td>
    `;
  }

  // Navigation
  document.getElementById('prevMonth').addEventListener('click', () => {
    currentDate.setMonth(currentDate.getMonth() - 1);
    renderCalendar();
  });

  document.getElementById('nextMonth').addEventListener('click', () => {
    currentDate.setMonth(currentDate.getMonth() + 1);
    renderCalendar();
  });

  // Initial render
  renderCalendar();
</script>

</body>
</html>

```
