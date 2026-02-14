# Rust Simple Web Application

This project demonstrates how to install Rust, build a simple web application, run it, and configure it as a system service on Ubuntu/Linux. The application exposes a web endpoint accessible via a browser.

---

# Project Structure

```
rust-simple-web/
│
├── Cargo.toml
├── README.md
└── src/
    └── main.rs
```

---

# Prerequisites

Ubuntu 20.04 / 22.04 / EC2 Ubuntu instance

Install required system dependencies:

```bash
sudo apt update
sudo apt install -y build-essential pkg-config libssl-dev curl git
```

Verify compiler installation:

```bash
cc --version
```

---

# Install Rust

Install Rust using rustup:

```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

Select option:

```
1) Proceed with installation (default)
```

Load Rust environment:

```bash
source $HOME/.cargo/env
```

Verify installation:

```bash
rustc --version
cargo --version
```

---

# Create Project

Create new Rust project:

```bash
cargo new rust-simple-web
cd rust-simple-web
```

---

# Configure Dependencies

Edit Cargo.toml:

```toml
[package]
name = "rust-simple-web"
version = "0.1.0"
edition = "2021"

[dependencies]
actix-web = "4"
```

---

# Application Source Code

Create file: src/main.rs

```rust
use actix_web::{get, App, HttpResponse, HttpServer, Responder};

#[get("/")]
async fn home() -> impl Responder {
    HttpResponse::Ok()
        .content_type("text/html")
        .body(
            r#"
            <html>
                <head>
                    <title>Rust Web App</title>
                </head>
                <body>
                    <h1>Hey Sowmiya, Your Rust Application is Running</h1>
                    <p>Status: Healthy</p>
                </body>
            </html>
            "#,
        )
}

#[get("/health")]
async fn health() -> impl Responder {
    HttpResponse::Ok().body("OK")
}

#[actix_web::main]
async fn main() -> std::io::Result<()> {
    println!("Server running at http://0.0.0.0:8080");

    HttpServer::new(|| {
        App::new()
            .service(home)
            .service(health)
    })
    .bind("0.0.0.0:8080")?
    .run()
    .await
}
```

---

# Run Application (Development Mode)

```bash
cargo run
```

Access in browser:

```
http://localhost:8080
```

Health check:

```
http://localhost:8080/health
```

---

# Build Application (Production Mode)

```bash
cargo build --release
```

Binary location:

```
target/release/rust-simple-web
```

Run binary:

```bash
./target/release/rust-simple-web
```

---

# Run Application in Background

```bash
nohup ./target/release/rust-simple-web > app.log 2>&1 &
```

Check running process:

```bash
ps aux | grep rust-simple-web
```

View logs:

```bash
tail -f app.log
```

---

# Create systemd Service (Recommended for Production)

Create service file:

```bash
sudo nano /etc/systemd/system/rust-simple-web.service
```

Add:

```ini
[Unit]
Description=Rust Simple Web Application
After=network.target

[Service]
ExecStart=/home/ubuntu/rust-simple-web/target/release/rust-simple-web
Restart=always
User=ubuntu

[Install]
WantedBy=multi-user.target
```

Reload systemd:

```bash
sudo systemctl daemon-reexec
sudo systemctl daemon-reload
```

Enable service:

```bash
sudo systemctl enable rust-simple-web
```

Start service:

```bash
sudo systemctl start rust-simple-web
```

Check service status:

```bash
sudo systemctl status rust-simple-web
```

View logs:

```bash
journalctl -u rust-simple-web -f
```

---

# Access Application

Browser:

```
http://SERVER-IP:8080
```

Example:

```
http://54.12.34.56:8080
```

---

# Firewall Configuration (AWS EC2)

Open port 8080 in Security Group:

```
Type: Custom TCP
Port: 8080
Source: 0.0.0.0/0
```

---

# Stop Service

```bash
sudo systemctl stop rust-simple-web
```

Disable service:

```bash
sudo systemctl disable rust-simple-web
```

---

# Summary

This setup provides:

* Rust installation
* Application build
* Production binary
* Background execution
* Auto-start service using systemd
* Web access via browser

---

# Author

DevOps Rust Deployment Example
