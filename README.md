# Multi-EC2 Web Server Deployment with Ansible and AWS Load Balancer

Automated deployment of a multi-node web server infrastructure on AWS using **Ansible**, with **Apache2 + PHP**, and **load balancing** via an **AWS Application Load Balancer (ALB)**. This project demonstrates infrastructure automation, orchestration, and basic cloud networking.

---

## Architecture Overview

```

```
      ┌───────────────┐
      │ Client Browser│
      └───────┬───────┘
              │
              ▼
      ┌─────────────────┐
      │  AWS ALB (80)   │
      │ Application LB  │
      └───────┬─────────┘
              │
  ┌───────────┼───────────┐
  │           │           │
  ▼           ▼           ▼
```

┌────────┐  ┌────────┐  ┌────────┐
│  EC2   │  │  EC2   │  │  EC2   │
│ web1   │  │ web2   │  │ web3   │
│Apache2 │  │Apache2 │  │Apache2 │
│ + PHP  │  │ + PHP  │  │ + PHP  │
└────────┘  └────────┘  └────────┘

Photos Of The Project : 

https://ibb.co/WpqH5YBF
https://ibb.co/3m0zNz2V
https://ibb.co/dskj1sKb
https://ibb.co/Ld0XL6FH




````

**Explanation:**  
- **Client Browser** sends requests to the **AWS ALB**.  
- The **ALB** distributes HTTP requests across the **3 EC2 web servers**.  
- Each EC2 server runs **Apache2 + PHP**, serving the web content.  
- **Health checks** on the target group ensure only healthy instances handle requests.  

---

## AWS Components

| Component              | Details |
|------------------------|---------|
| EC2 Instances          | 3 Ubuntu 22.04 LTS, t3.micro, eu-north-1b |
| Load Balancer          | Application Load Balancer, HTTP 80 |
| Target Group           | HTTP, port 80, attached to EC2 instances |
| Security Groups        | Allow SSH (22) and HTTP (80) from trusted sources |
| SSH Authentication     | Key-based via `/home/eissa/Downloads/key.pem` |

**Tips:**  
- Confirm EC2 instances are healthy in the Target Group before accessing via ALB.  
- Ensure security groups allow HTTP traffic to ALB and SSH to your machine.  

---

## Ansible Configuration

### Inventory File (`inventory`)

```ini
[web]
16.171.175.186
13.61.32.175
51.20.37.84

[web:vars]
ansible_user=ubuntu
ansible_ssh_private_key_file=/home/eissa/Downloads/key.pem
````

### Ansible Config (`ansible.cfg`)

```ini
[defaults]
inventory = inventory
private_key_file = /home/eissa/Downloads/key.pem
```

### Playbook (`apache.yml`)

```yaml
---
- hosts: all
  become: true
  tasks:
    - name: Update APT cache
      apt:
        update_cache: yes
        cache_valid_time: 3600

    - name: Install Apache2
      apt:
        name: apache2
        state: latest

    - name: Install PHP module for Apache
      apt:
        name: libapache2-mod-php
        state: latest

    - name: Ensure Apache is started and enabled
      service:
        name: apache2
        state: started
        enabled: true
```

---

## How to Run

1. Clone this repository:

```bash
git clone <repo-url>
cd <repo-folder>
```

2. Verify SSH access to all EC2 instances:

```bash
ansible all -m ping
```

3. Run the playbook:

```bash
ansible-playbook apache.yml
```

4. Access your web application via the **ALB DNS name** in a browser.

---

## Features Demonstrated

* Multi-node orchestration using **Ansible**
* Automated **Apache2 + PHP** deployment
* AWS **Application Load Balancer** with target group health checks
* SSH key-based authentication for secure connections
* Inventory management for multiple hosts

---

## Next Steps / Enhancements

* Convert playbook into **roles** for modularity
* Enable **HTTPS/TLS** on ALB
* Integrate **Jenkins CI/CD pipeline** for full automation
* Add **monitoring and alerting** (CloudWatch, Netdata, Prometheus)
* Expand infrastructure to **multi-region** or multiple Availability Zones

---

## Author

**Eissa** – LFCS Certified Linux SysAdmin | Cloud Automation Enthusiast
[GitHub](https://github.com/AbdullrahmanEissa)

---

## License

MIT License – see [LICENSE](LICENSE)

```
Do you want me to do that next?
```
