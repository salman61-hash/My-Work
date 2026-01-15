```bash
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Pricing - Modern Cards</title>

  <!-- Bootstrap 5 CSS -->
  <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" rel="stylesheet">

  <!-- Bootstrap Icons -->
  <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap-icons@1.11.3/font/bootstrap-icons.min.css">

  <style>
    body {
      background: #f8f9fa;
      font-family: system-ui, -apple-system, sans-serif;
    }

    .pricing-card {
      background: white;
      border-radius: 1.25rem;
      overflow: hidden;
      border: none;
      box-shadow: 0 10px 30px rgba(0,0,0,0.08);
      transition: all 0.4s ease;
      position: relative;
    }

    .pricing-card:hover {
      transform: translateY(-14px);
      box-shadow: 0 25px 60px rgba(0,0,0,0.14);
    }

    .accent-bar {
      height: 8px;
      width: 100%;
    }

    .price {
      font-size: 3.8rem;
      font-weight: 800;
      line-height: 0.9;
      color: #0d6efd;
    }

    .period {
      font-size: 1.1rem;
      color: #6c757d;
    }

    .feature-list li {
      margin-bottom: 1rem;
      font-size: 1.05rem;
    }

    .feature-list i.bi-check-circle-fill {
      color: #28a745;
      font-size: 1.3rem;
      margin-right: 12px;
    }

    .feature-list i.bi-x-circle {
      color: #dc3545;
      font-size: 1.3rem;
      margin-right: 12px;
    }

    .btn-choose {
      padding: 0.85rem 2rem;
      font-size: 1.1rem;
      font-weight: 600;
      border-radius: 50rem;
      transition: all 0.3s;
    }

    .btn-outline-primary.btn-choose:hover {
      background-color: #0d6efd;
      color: white;
      transform: translateY(-2px);
    }

    .recommended .pricing-card {
      border: 3px solid #0d6efd;
      box-shadow: 0 15px 40px rgba(13, 110, 253, 0.18);
    }

    .recommended .accent-bar {
      background: linear-gradient(90deg, #0d6efd, #6610f2);
    }

    .popular-badge {
      position: absolute;
      top: 18px;
      right: 18px;
      background: #0d6efd;
      color: white;
      padding: 0.4rem 1.1rem;
      border-radius: 50rem;
      font-size: 0.9rem;
      font-weight: 600;
      z-index: 10;
    }
  </style>
</head>
<body>

<section class="py-5 py-lg-5">
  <div class="container">

    <div class="text-center mb-5 pb-3">
      <h1 class="fw-bold display-4">Simple, Transparent Pricing</h1>
      <p class="lead text-muted mx-auto" style="max-width: 620px;">
        Choose the plan that best fits your learning goals and budget
      </p>
    </div>

    <div class="row g-4 g-lg-5 justify-content-center">

      <!-- STANDARD -->
      <div class="col-lg-4 col-md-6">
        <div class="pricing-card h-100">
          <div class="accent-bar bg-primary"></div>

          <div class="p-4 p-xl-5 text-center d-flex flex-column h-100">
            <h3 class="fw-bold mb-2">Standard</h3>
            <p class="text-muted mb-4">Perfect for beginners</p>

            <div class="mb-5">
              <span class="price">$49</span>
              <span class="period">/month</span>
            </div>

            <ul class="feature-list list-unstyled text-start flex-grow-1 mb-5">
              <li><i class="bi bi-check-circle-fill"></i> 2 Courses of choice</li>
              <li><i class="bi bi-check-circle-fill"></i> 30 Days Access</li>
              <li><i class="bi bi-check-circle-fill"></i> Certificate on Completion</li>
              <li><i class="bi bi-check-circle-fill"></i> All Quizzes & Assignments</li>
              <li><i class="bi bi-check-circle-fill"></i> Downloadable Materials</li>
              <li><i class="bi bi-x-circle"></i> <span class="text-muted">Live Sessions</span></li>
              <li><i class="bi bi-x-circle"></i> <span class="text-muted">Portfolio Review</span></li>
            </ul>

            <div class="mt-auto">
              <button class="btn btn-outline-primary btn-choose w-100">
                Choose Standard
              </button>
            </div>
          </div>
        </div>
      </div>

      <!-- PREMIUM - Recommended -->
      <div class="col-lg-4 col-md-6 recommended">
        <div class="popular-badge">Most Popular</div>
        <div class="pricing-card h-100">
          <div class="accent-bar"></div>

          <div class="p-4 p-xl-5 text-center d-flex flex-column h-100">
            <h3 class="fw-bold mb-2">Premium</h3>
            <p class="text-muted mb-4">Best value for serious learners</p>

            <div class="mb-5">
              <span class="price">$99</span>
              <span class="period">/month</span>
            </div>

            <ul class="feature-list list-unstyled text-start flex-grow-1 mb-5">
              <li><i class="bi bi-check-circle-fill"></i> 5 Courses of choice</li>
              <li><i class="bi bi-check-circle-fill"></i> 90 Days Access</li>
              <li><i class="bi bi-check-circle-fill"></i> Certificate on Completion</li>
              <li><i class="bi bi-check-circle-fill"></i> All Quizzes & Assignments</li>
              <li><i class="bi bi-check-circle-fill"></i> Downloadable Materials</li>
              <li><i class="bi bi-check-circle-fill"></i> Exclusive Live Sessions</li>
              <li><i class="bi bi-check-circle-fill"></i> Resume & Portfolio Review</li>
            </ul>

            <div class="mt-auto">
              <button class="btn btn-primary btn-choose w-100 text-white">
                Choose Premium
              </button>
            </div>
          </div>
        </div>
      </div>

      <!-- ENTERPRISE -->
      <div class="col-lg-4 col-md-6">
        <div class="pricing-card h-100">
          <div class="accent-bar bg-dark"></div>

          <div class="p-4 p-xl-5 text-center d-flex flex-column h-100">
            <h3 class="fw-bold mb-2">Enterprise</h3>
            <p class="text-muted mb-4">For teams & professionals</p>

            <div class="mb-5">
              <span class="price">$249</span>
              <span class="period">/month</span>
            </div>

            <ul class="feature-list list-unstyled text-start flex-grow-1 mb-5">
              <li><i class="bi bi-check-circle-fill"></i> 8+ Courses of choice</li>
              <li><i class="bi bi-check-circle-fill"></i> 1 Year Full Access</li>
              <li><i class="bi bi-check-circle-fill"></i> Certificate on Completion</li>
              <li><i class="bi bi-check-circle-fill"></i> All Quizzes & Assignments</li>
              <li><i class="bi bi-check-circle-fill"></i> Downloadable Materials</li>
              <li><i class="bi bi-check-circle-fill"></i> Priority Live Sessions</li>
              <li><i class="bi bi-check-circle-fill"></i> Resume & Portfolio Review</li>
            </ul>

            <div class="mt-auto">
              <button class="btn btn-outline-dark btn-choose w-100">
                Choose Enterprise
              </button>
            </div>
          </div>
        </div>
      </div>

    </div>
  </div>
</section>

</body>
</html>

```
