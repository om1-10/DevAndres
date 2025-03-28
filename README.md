# DevAndres
This is my first repo lets make it better
<br>
Suthor - Om suryavanshi 


<?php
session_start();
$conn = new mysqli("localhost", "root", "", "healthcare_db");

if ($conn->connect_error) {
    die("Connection failed: " . $conn->connect_error);
}

// Redirect to login if not logged in
if (!isset($_SESSION["doctor_id"])) {
    header("Location: login.php");
    exit();
}

// Fetch patients data
$patients = $conn->query("SELECT * FROM patients ORDER BY created_at DESC");
?>

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Doctor Dashboard</title>
    <link rel="stylesheet" href="style.css">
</head>
<body>

<header>
    <h2>Welcome, Dr. <?= htmlspecialchars($_SESSION["doctor_name"]) ?></h2>
    <nav>
        <a href="dashboard.php">Home</a> |
        <a href="logout.php" class="logout-btn">Logout</a>
    </nav>
</header>

<div class="dashboard-container">
    <!-- Prescription Form -->
    <form action="save.php" method="POST" class="prescription-form">
        <h3>New Prescription</h3>
        <input type="text" name="name" placeholder="Patient Name" required>
        <input type="number" name="age" placeholder="Age" required>
        <textarea name="medical_history" placeholder="Medical History" required></textarea>
        <textarea name="allergies" placeholder="Allergies"></textarea>
        <textarea name="prescription" placeholder="Prescription" required></textarea>
        <button type="submit">Save Prescription</button>
    </form>

    <!-- Patients List -->
    <div class="patients-list">
        <h3>Recent Patients</h3>
        <?php while ($patient = $patients->fetch_assoc()): ?>
            <div class="patient-card">
                <h4><?= htmlspecialchars($patient["name"]) ?> (<?= htmlspecialchars($patient["age"]) ?>)</h4>
                <p><strong>Prescription:</strong> <?= htmlspecialchars($patient["prescription"]) ?></p>
            </div>
        <?php endwhile; ?>
    </div>
</div>

</body>
</html>

dashboard.php 



<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>HealthCare Pro - Doctor Portal</title>
    <link rel="stylesheet" href="style.css">
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Poppins:wght@300;400;500;600;700&display=swap" rel="stylesheet">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
</head>
<body>
    <?php
    session_start();
    
    // Check if user is already logged in
    if (isset($_SESSION["doctor_id"])) {
        header("Location: dashboard.php");
        exit();
    }
    
    // Check if there's a signup success message
    $signup_success = isset($_GET['signup']) && $_GET['signup'] === 'success';
    ?>
    
    <div class="landing-container">
        <div class="hero-section">
            <div class="hero-content">
                <h1><i class="fas fa-heartbeat"></i> HealthCare Pro</h1>
                <p class="subtitle">Advanced prescription management for medical professionals</p>
                
                <div class="auth-box">
                    <?php if ($signup_success): ?>
                        <div class="success-message">
                            <i class="fas fa-check-circle"></i> Registration successful! Please login.
                        </div>
                    <?php endif; ?>
                    
                    <h2>Doctor Portal</h2>
                    
                    <div class="auth-options">
                        <a href="login.php" class="auth-btn login-btn">
                            <i class="fas fa-sign-in-alt"></i> Login
                        </a>
                        <a href="signup.php" class="auth-btn signup-btn">
                            <i class="fas fa-user-plus"></i> Register
                        </a>
                    </div>
                </div>
            </div>
            
            <div class="hero-image">
                <img src="https://images.unsplash.com/photo-1579684385127-1ef15d508118?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=880&q=80" alt="Doctor with patient">
            </div>
        </div>
        
        <div class="features-section">
            <h2>Why Choose HealthCare Pro?</h2>
            <div class="features-grid">
                <div class="feature-card">
                    <i class="fas fa-prescription-bottle-alt"></i>
                    <h3>Digital Prescriptions</h3>
                    <p>Create, manage, and track prescriptions electronically with our secure platform.</p>
                </div>
                
                <div class="feature-card">
                    <i class="fas fa-history"></i>
                    <h3>Patient History</h3>
                    <p>Access complete medical histories and previous prescriptions at a glance.</p>
                </div>
                
                <div class="feature-card">
                    <i class="fas fa-shield-alt"></i>
                    <h3>Secure & HIPAA Compliant</h3>
                    <p>Your patient data is protected with enterprise-grade security measures.</p>
                </div>
            </div>
        </div>
        
        <footer>
            <p>&copy; 2023 HealthCare Pro. All rights reserved.</p>
            <div class="footer-links">
                <a href="#">Privacy Policy</a>
                <a href="#">Terms of Service</a>
                <a href="#">Contact Support</a>
            </div>
        </footer>
    </div>
</body>
</html>

index.html



<?php
// Redirect to login page by default
header("Location: login.php");
exit();
?>

index.php




<?php
session_start();
$conn = new mysqli("localhost", "root", "", "healthcare_db");

if ($conn->connect_error) {
    die("Connection failed: " . $conn->connect_error);
}

$error = "";
if ($_SERVER["REQUEST_METHOD"] == "POST") {
    $email = $_POST["email"];
    $password = $_POST["password"];

    $stmt = $conn->prepare("SELECT id, name, password FROM doctors WHERE email = ?");
    $stmt->bind_param("s", $email);
    $stmt->execute();
    $result = $stmt->get_result();

    if ($result->num_rows == 1) {
        $doctor = $result->fetch_assoc();
        if (password_verify($password, $doctor["password"])) {
            $_SESSION["doctor_id"] = $doctor["id"];
            $_SESSION["doctor_name"] = $doctor["name"];
            header("Location: dashboard.php");
            exit();
        } else {
            $error = "Invalid email or password!";
        }
    } else {
        $error = "Invalid email or password!";
    }
}
?>

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Doctor Login</title>
    <link rel="stylesheet" href="style.css">
</head>
<body>
    <div class="auth-container">
        <h2>Doctor Login</h2>
        <?php if (isset($_GET["signup"]) && $_GET["signup"] == "success"): ?>
            <div class="success">Registration successful! Please login.</div>
        <?php endif; ?>
        
        <?php if ($error): ?>
            <div class="error"><?= $error ?></div>
        <?php endif; ?>
        
        <form action="login.php" method="POST">
            <input type="email" name="email" placeholder="Email" required>
            <input type="password" name="password" placeholder="Password" required>
            <button type="submit">Login</button>
        </form>
        <p>Don't have an account? <a href="signup.php">Sign up here</a></p>
    </div>
</body>
</html>
login.php



<?php
session_start();
session_unset();
session_destroy();
header("Location: login.php");
exit();
?>
logout.php



<?php
require('fpdf/fpdf.php');

// ✅ 1️⃣ Create the Folder If It Doesn't Exist
$folder = 'prescriptions/';
if (!file_exists($folder)) {
    mkdir($folder, 0777, true); // Ensure folder exists with correct permissions
}

// ✅ 2️⃣ Database Connection
$conn = new mysqli("localhost", "root", "", "healthcare_db");
if ($conn->connect_error) {
    die("Database Connection Failed: " . $conn->connect_error);
}

// ✅ 3️⃣ Fetch & Secure Form Data
$name = isset($_POST["name"]) ? trim(htmlspecialchars($_POST["name"])) : "Unknown";
$age = isset($_POST["age"]) ? intval($_POST["age"]) : 0;
$history = isset($_POST["history"]) ? trim(htmlspecialchars($_POST["history"])) : "N/A";
$allergies = isset($_POST["allergies"]) ? trim(htmlspecialchars($_POST["allergies"])) : "None";
$prescription = isset($_POST["prescription"]) ? trim(htmlspecialchars($_POST["prescription"])) : "N/A";

// ✅ 4️⃣ Save Data to Database (Fixed Column Name "medical_history")
$stmt = $conn->prepare("INSERT INTO patients (name, age, medical_history, allergies, prescription) VALUES (?, ?, ?, ?, ?)");
if ($stmt) {
    $stmt->bind_param("sisss", $name, $age, $history, $allergies, $prescription);
    $stmt->execute();
    $stmt->close();
} else {
    die("Database Error: " . $conn->error);
}

// ✅ 5️⃣ Generate PDF
class PDF extends FPDF {
    function Header() {
        $this->SetFont('Arial', 'B', 14);
        $this->Cell(190, 10, 'Medical Prescription', 0, 1, 'C');
        $this->Ln(10);
    }
}

$pdf = new PDF();
$pdf->AddPage();
$pdf->SetFont('Arial', '', 12);
$pdf->Cell(40, 10, "Patient Name: " . $name);
$pdf->Ln();
$pdf->Cell(40, 10, "Age: " . $age);
$pdf->Ln();
$pdf->Cell(40, 10, "Medical History: " . $history);
$pdf->Ln();
$pdf->Cell(40, 10, "Allergies: " . $allergies);
$pdf->Ln();
$pdf->Cell(40, 10, "Prescription: " . $prescription);
$pdf->Ln(10);

// ✅ 6️⃣ Save PDF
$pdfFileName = $folder . str_replace(" ", "_", $name) . "_prescription.pdf";
$pdf->Output("F", $pdfFileName);

// ✅ 7️⃣ Show Download Link
echo "✅ Prescription PDF Generated: <a href='$pdfFileName' target='_blank'>Download Here</a>";

// ✅ 8️⃣ Close Database Connection
$conn->close();
?>
save.php




<?php
session_start();
$conn = new mysqli("localhost", "root", "", "healthcare_db");

if ($conn->connect_error) {
    die("Connection failed: " . $conn->connect_error);
}

$error = "";
if ($_SERVER["REQUEST_METHOD"] == "POST") {
    $name = $_POST["name"];
    $email = $_POST["email"];
    $password = password_hash($_POST["password"], PASSWORD_DEFAULT);
    $specialization = $_POST["specialization"];

    // Check if email already exists
    $check = $conn->prepare("SELECT id FROM doctors WHERE email = ?");
    $check->bind_param("s", $email);
    $check->execute();
    $check->store_result();

    if ($check->num_rows > 0) {
        $error = "Email already registered!";
    } else {
        $stmt = $conn->prepare("INSERT INTO doctors (name, email, password, specialization) VALUES (?, ?, ?, ?)");
        $stmt->bind_param("ssss", $name, $email, $password, $specialization);
        
        if ($stmt->execute()) {
            header("Location: login.php?signup=success");
            exit();
        } else {
            $error = "Registration failed!";
        }
    }
}
?>

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Doctor Signup</title>
    <link rel="stylesheet" href="style.css">
</head>
<body>
    <div class="auth-container">
        <h2>Doctor Registration</h2>
        <?php if ($error): ?>
            <div class="error"><?= $error ?></div>
        <?php endif; ?>
        
        <form action="signup.php" method="POST">
            <input type="text" name="name" placeholder="Full Name" required>
            <input type="email" name="email" placeholder="Email" required>
            <input type="password" name="password" placeholder="Password" required>
            <input type="text" name="specialization" placeholder="Specialization" required>
            <button type="submit">Register</button>
        </form>
        <p>Already have an account? <a href="login.php">Login here</a></p>
    </div>
</body>
</html>
signup.php



 /* General Styles */
:root {
    --primary-color: #2a7f8c;
    --secondary-color: #3ab1c4;
    --accent-color: #f85a40;
    --light-bg: #f5f9fa;
    --dark-text: #2d3748;
    --light-text: #f8f9fa;
    --border-radius: 8px;
    --box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  }
  
  * {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  }
  
  body {
    background-color: var(--light-bg);
    color: var(--dark-text);
    line-height: 1.6;
  }
  
  /* Header Styles */
  header {
    background-color: white;
    padding: 1rem 2rem;
    box-shadow: var(--box-shadow);
    display: flex;
    justify-content: space-between;
    align-items: center;
    position: sticky;
    top: 0;
    z-index: 100;
  }
  
  header h2 {
    color: var(--primary-color);
    font-weight: 600;
  }
  
  nav a {
    text-decoration: none;
    color: var(--dark-text);
    margin: 0 0.5rem;
    padding: 0.5rem 1rem;
    border-radius: var(--border-radius);
    transition: all 0.3s ease;
  }
  
  nav a:hover {
    color: var(--primary-color);
    background-color: rgba(42, 127, 140, 0.1);
  }
  
  .logout-btn {
    color: var(--accent-color) !important;
    border: 1px solid var(--accent-color);
  }
  
  .logout-btn:hover {
    background-color: var(--accent-color) !important;
    color: white !important;
  }
  
  /* Auth Container Styles */
  .auth-container {
    max-width: 450px;
    margin: 5rem auto;
    padding: 2.5rem;
    background-color: white;
    border-radius: var(--border-radius);
    box-shadow: var(--box-shadow);
    text-align: center;
  }
  
  .auth-container h2 {
    color: var(--primary-color);
    margin-bottom: 1.5rem;
    font-size: 1.8rem;
  }
  
  .auth-container form {
    display: flex;
    flex-direction: column;
    gap: 1rem;
  }
  
  .auth-container input {
    padding: 0.8rem 1rem;
    border: 1px solid #ddd;
    border-radius: var(--border-radius);
    font-size: 1rem;
    transition: border 0.3s ease;
  }
  
  .auth-container input:focus {
    outline: none;
    border-color: var(--secondary-color);
  }
  
  .auth-container button {
    background-color: var(--primary-color);
    color: white;
    padding: 0.8rem;
    border: none;
    border-radius: var(--border-radius);
    font-size: 1rem;
    font-weight: 600;
    cursor: pointer;
    transition: background-color 0.3s ease;
  }
  
  .auth-container button:hover {
    background-color: var(--secondary-color);
  }
  
  .auth-container p {
    margin-top: 1.5rem;
    color: var(--dark-text);
  }
  
  .auth-container a {
    color: var(--primary-color);
    text-decoration: none;
    font-weight: 600;
  }
  
  .auth-container a:hover {
    text-decoration: underline;
  }
  
  .error {
    background-color: #fee2e2;
    color: #b91c1c;
    padding: 0.8rem;
    border-radius: var(--border-radius);
    margin-bottom: 1rem;
    font-size: 0.9rem;
  }
  
  .success {
    background-color: #dcfce7;
    color: #166534;
    padding: 0.8rem;
    border-radius: var(--border-radius);
    margin-bottom: 1rem;
    font-size: 0.9rem;
  }
  
  /* Dashboard Styles */
  .dashboard-container {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 2rem;
    padding: 2rem;
    max-width: 1400px;
    margin: 0 auto;
  }
  
  @media (max-width: 1024px) {
    .dashboard-container {
      grid-template-columns: 1fr;
    }
  }
  
  .prescription-form {
    background-color: white;
    padding: 1.5rem;
    border-radius: var(--border-radius);
    box-shadow: var(--box-shadow);
  }
  
  .prescription-form h3 {
    color: var(--primary-color);
    margin-bottom: 1.5rem;
    font-size: 1.3rem;
    border-bottom: 2px solid var(--light-bg);
    padding-bottom: 0.5rem;
  }
  
  .prescription-form input,
  .prescription-form textarea {
    width: 100%;
    padding: 0.8rem;
    margin-bottom: 1rem;
    border: 1px solid #ddd;
    border-radius: var(--border-radius);
    font-size: 1rem;
    transition: all 0.3s ease;
  }
  
  .prescription-form textarea {
    min-height: 100px;
    resize: vertical;
  }
  
  .prescription-form input:focus,
  .prescription-form textarea:focus {
    outline: none;
    border-color: var(--secondary-color);
    box-shadow: 0 0 0 2px rgba(58, 177, 196, 0.2);
  }
  
  .prescription-form button {
    background-color: var(--primary-color);
    color: white;
    padding: 0.8rem 1.5rem;
    border: none;
    border-radius: var(--border-radius);
    font-size: 1rem;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
    width: 100%;
  }
  
  .prescription-form button:hover {
    background-color: var(--secondary-color);
    transform: translateY(-2px);
  }
  
  .patients-list {
    background-color: white;
    padding: 1.5rem;
    border-radius: var(--border-radius);
    box-shadow: var(--box-shadow);
    max-height: 80vh;
    overflow-y: auto;
  }
  
  .patients-list h3 {
    color: var(--primary-color);
    margin-bottom: 1.5rem;
    font-size: 1.3rem;
    border-bottom: 2px solid var(--light-bg);
    padding-bottom: 0.5rem;
    position: sticky;
    top: 0;
    background-color: white;
    z-index: 1;
  }
  
  .patient-card {
    background-color: var(--light-bg);
    padding: 1rem;
    border-radius: var(--border-radius);
    margin-bottom: 1rem;
    transition: all 0.3s ease;
  }
  
  .patient-card:hover {
    transform: translateX(5px);
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  }
  
  .patient-card h4 {
    color: var(--primary-color);
    margin-bottom: 0.5rem;
  }
  
  .patient-card p {
    color: var(--dark-text);
    font-size: 0.9rem;
  }
  
  /* Animations */
  @keyframes fadeIn {
    from { opacity: 0; transform: translateY(10px); }
    to { opacity: 1; transform: translateY(0); }
  }
  
  .auth-container, .dashboard-container {
    animation: fadeIn 0.5s ease-out;
  }
  
  .patient-card {
    animation: fadeIn 0.3s ease-out;
    animation-fill-mode: both;
  }
  
  .patient-card:nth-child(1) { animation-delay: 0.1s; }
  .patient-card:nth-child(2) { animation-delay: 0.2s; }
  .patient-card:nth-child(3) { animation-delay: 0.3s; }
  /* Add more as needed */

  /* Landing Page Styles */
.landing-container {
    max-width: 1200px;
    margin: 0 auto;
    padding: 0 1rem;
}

.hero-section {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 2rem;
    align-items: center;
    margin: 3rem 0 5rem;
}

@media (max-width: 768px) {
    .hero-section {
        grid-template-columns: 1fr;
    }
}

.hero-content h1 {
    font-size: 2.5rem;
    color: var(--primary-color);
    margin-bottom: 1rem;
}

.hero-content h1 i {
    margin-right: 0.5rem;
}

.subtitle {
    font-size: 1.2rem;
    color: var(--dark-text);
    margin-bottom: 2rem;
    opacity: 0.8;
}

.hero-image img {
    width: 100%;
    border-radius: var(--border-radius);
    box-shadow: var(--box-shadow);
}

.auth-box {
    background-color: white;
    padding: 2rem;
    border-radius: var(--border-radius);
    box-shadow: var(--box-shadow);
    margin-top: 2rem;
}

.auth-box h2 {
    text-align: center;
    margin-bottom: 1.5rem;
    color: var(--primary-color);
}

.auth-options {
    display: flex;
    gap: 1rem;
}

.auth-btn {
    flex: 1;
    padding: 1rem;
    text-align: center;
    border-radius: var(--border-radius);
    text-decoration: none;
    font-weight: 600;
    transition: all 0.3s ease;
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 0.5rem;
}

.login-btn {
    background-color: var(--primary-color);
    color: white;
}

.login-btn:hover {
    background-color: var(--secondary-color);
    transform: translateY(-2px);
}

.signup-btn {
    background-color: white;
    color: var(--primary-color);
    border: 2px solid var(--primary-color);
}

.signup-btn:hover {
    background-color: var(--light-bg);
    transform: translateY(-2px);
}

.success-message {
    background-color: #dcfce7;
    color: #166534;
    padding: 1rem;
    border-radius: var(--border-radius);
    margin-bottom: 1.5rem;
    display: flex;
    align-items: center;
    gap: 0.5rem;
}

.success-message i {
    font-size: 1.2rem;
}

/* Features Section */
.features-section {
    margin: 5rem 0;
    text-align: center;
}

.features-section h2 {
    font-size: 2rem;
    color: var(--primary-color);
    margin-bottom: 3rem;
}

.features-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
    gap: 2rem;
}

.feature-card {
    background-color: white;
    padding: 2rem;
    border-radius: var(--border-radius);
    box-shadow: var(--box-shadow);
    transition: all 0.3s ease;
}

.feature-card:hover {
    transform: translateY(-5px);
    box-shadow: 0 10px 20px rgba(0, 0, 0, 0.1);
}

.feature-card i {
    font-size: 2.5rem;
    color: var(--primary-color);
    margin-bottom: 1.5rem;
}

.feature-card h3 {
    margin-bottom: 1rem;
    color: var(--primary-color);
}

.feature-card p {
    color: var(--dark-text);
    opacity: 0.8;
}

/* Footer */
footer {
    text-align: center;
    margin: 5rem 0 2rem;
    padding-top: 2rem;
    border-top: 1px solid #ddd;
    color: var(--dark-text);
    opacity: 0.7;
}

.footer-links {
    margin-top: 1rem;
    display: flex;
    justify-content: center;
    gap: 1.5rem;
}

.footer-links a {
    color: var(--primary-color);
    text-decoration: none;
}

.footer-links a:hover {
    text-decoration: underline;
}
style.css
