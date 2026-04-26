# 1. Create a folder
mkdir stockton-porta-potties
cd stockton-porta-potties

# 2. Create the files (copy from below)

RewriteEngine On
RewriteBase /
RewriteCond %{REQUEST_FILENAME} -f [OR]
RewriteCond %{REQUEST_FILENAME} -d
RewriteRule ^ - [L]
RewriteRule ^ index.html [L]

<IfModule mod_deflate.c>
  AddOutputFilterByType DEFLATE text/html text/css text/javascript application/javascript
</IfModule>

<IfModule mod_expires.c>
  ExpiresActive On
  ExpiresByType image/jpeg "access plus 1 year"
  ExpiresByType image/png "access plus 1 year"
  ExpiresByType text/css "access plus 1 month"
  ExpiresByType application/javascript "access plus 1 month"
</IfModule>
<?php
header('Content-Type: application/json');
header('Access-Control-Allow-Origin: *');

if ($_SERVER['REQUEST_METHOD'] !== 'POST') {
    http_response_code(405);
    echo json_encode(['success' => false]);
    exit;
}
$raw = file_get_contents('php://input');
$data = json_decode($raw, true);
if (!$data) $data = $_POST;

$name = trim($data['name'] ?? '');
$phone = trim($data['phone'] ?? '');
if (empty($name) || empty($phone)) {
    echo json_encode(['success' => false, 'error' => 'Name and phone required']);
    exit;
}

$adminEmail = 'epalacio@ymail.com';
$subject = "New Lead: $name";

$html = "<h2>New Quote Request</h2>";
$html .= "<p><strong>Name:</strong> " . htmlspecialchars($name) . "</p>";
$html .= "<p><strong>Phone:</strong> " . htmlspecialchars($phone) . "</p>";
if (!empty($data['email'])) $html .= "<p><strong>Email:</strong> " . htmlspecialchars($data['email']) . "</p>";
if (!empty($data['eventType'])) $html .= "<p><strong>Service:</strong> " . htmlspecialchars($data['eventType']) . "</p>";
if (!empty($data['location'])) $html .= "<p><strong>Location:</strong> " . htmlspecialchars($data['location']) . "</p>";
if (!empty($data['message'])) $html .= "<p><strong>Message:</strong> " . htmlspecialchars($data['message']) . "</p>";

$headers = "MIME-Version: 1.0\r\nContent-Type: text/html; charset=UTF-8\r\nFrom: Stockton Porta Potties <leads@stocktonportapotties.com>\r\n";

mail($adminEmail, $subject, $html, $headers);

echo json_encode(['success' => true]);
git init
git add .
git commit -m "Initial deployment"
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/stockton-porta-potties.git
git push -u origin main
