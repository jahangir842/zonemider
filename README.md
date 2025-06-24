# 📹 ZoneMinder Installation Guide on Ubuntu 22.04 LTS

## 📘 Overview

**ZoneMinder** is a powerful, open-source video surveillance application that supports IP cameras, USB webcams, and analog capture cards. This guide covers the complete installation and configuration process for ZoneMinder on **Ubuntu 22.04 LTS**, suitable for home or small office security setups.

---

## 🛠️ Prerequisites

* A system running Ubuntu 22.04 LTS
* Internet access for package installation
* Administrative (sudo) privileges

---

## 🧾 Step-by-Step Installation (Manual Method)

### 🔹 Step 1: Update System

```bash
sudo apt update
sudo apt upgrade -y
```

---

### 🔹 Step 2: Install Required Dependencies

```bash
sudo apt install software-properties-common lsb-release gnupg apache2 mariadb-server php libapache2-mod-php php-mysql php-gd php-xml php-mbstring php-bcmath -y
```

---

### 🔹 Step 3: Add Official ZoneMinder PPA (v1.36 for Ubuntu 22.04)

```bash
sudo add-apt-repository ppa:iconnor/zoneminder-1.36 -y
sudo apt update
```

---

### 🔹 Step 4: Install ZoneMinder

```bash
sudo apt install zoneminder -y
```

---

### 🔹 Step 5: Enable and Start ZoneMinder

```bash
sudo systemctl enable zoneminder
sudo systemctl start zoneminder
```

---

### 🔹 Step 6: Enable Apache Modules

```bash
sudo a2enmod rewrite
sudo a2enmod cgi
```

---

### 🔹 Step 7: Enable ZoneMinder Apache Configuration

```bash
sudo a2enconf zoneminder
```

---

### 🔹 Step 8: Restart Apache

```bash
sudo systemctl restart apache2
```

---

### 🔹 Step 9: Access the Web Interface

Open a web browser and navigate to:

```
http://<your-server-ip>/zm
```

Example (localhost):

```
http://localhost/zm
```

---

## 📦 Optional: Allow Web Traffic Through UFW (if enabled)

```bash
sudo ufw allow http
sudo ufw reload
```

---

## 📜 Logs and Troubleshooting

* Apache errors:

  ```bash
  sudo tail -n 100 /var/log/apache2/error.log
  ```

* ZoneMinder logs:

  ```bash
  sudo tail -n 100 /var/log/zm/zm*.log
  ```

* Service status:

  ```bash
  sudo systemctl status zoneminder
  ```

---

## ⚙️ Bonus: Ansible Playbook (Minimal Example)

```yaml
- name: Install ZoneMinder on Ubuntu 22.04
  hosts: zoneminder
  become: true
  tasks:
    - name: Add PPA for ZoneMinder 1.36
      ansible.builtin.apt_repository:
        repo: ppa:iconnor/zoneminder-1.36
        state: present

    - name: Install ZoneMinder and dependencies
      ansible.builtin.apt:
        name:
          - zoneminder
          - apache2
          - mariadb-server
          - php
          - libapache2-mod-php
          - php-mysql
          - php-gd
          - php-xml
          - php-mbstring
          - php-bcmath
        state: present
        update_cache: yes

    - name: Enable required Apache modules
      ansible.builtin.command: a2enmod rewrite && a2enmod cgi
      notify: Restart Apache

    - name: Enable ZoneMinder Apache configuration
      ansible.builtin.command: a2enconf zoneminder
      notify: Restart Apache

    - name: Enable and start ZoneMinder
      ansible.builtin.systemd:
        name: zoneminder
        enabled: true
        state: started

  handlers:
    - name: Restart Apache
      ansible.builtin.systemd:
        name: apache2
        state: restarted
```

---

## ✅ Final Checklist

| Task                              | Status |
| --------------------------------- | ------ |
| Ubuntu system updated             | ✅      |
| Dependencies installed            | ✅      |
| PPA added                         | ✅      |
| ZoneMinder installed              | ✅      |
| Apache modules enabled            | ✅      |
| Apache and ZoneMinder started     | ✅      |
| Web interface accessible at `/zm` | ✅      |

---
