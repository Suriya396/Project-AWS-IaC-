
## ✅ NGINX EC2 Web Server Provisioning Using AWS CLI / Terraform

### 🔧 Provisioned Infrastructure

* **VPC**: `10.0.0.0/16`
* **Public Subnet**: Created inside the VPC
* **Security Group Rules**:

  * Port **22 (SSH)**: Open to all (0.0.0.0/0)
  * Port **80 (HTTP)**: Open to all (0.0.0.0/0)
* **EC2 Instance**:

  * AMI: Amazon Linux 2
  * User Data Script: Installs and runs NGINX, sets custom HTML content

---






## Installation
### 🖥️ Commands Run Inside EC2 Instance (from screenshots)

```bash
# Check NGINX status
sudo systemctl status nginx

# Enable NGINX to start on boot
sudo systemctl enable nginx

# Set custom content in default HTML page
echo "<h1> Hello From Devops </h1>" | sudo tee /usr/share/nginx/html/index.html
```


>
> * `usr/share/nginx/index.html` 
               OR
`/usr/share/nginx/html/index.html`

## Output and Screenshot


### 📤 Outputs

| Resource  | Value                                                           |
| --------- | --------------------------------------------------------------- |
| Public IP | `54.196.229 17`                                                 |

---

![Screenshot 2025-05-07 114002](https://github.com/user-attachments/assets/23b13dd5-e843-481a-b1d0-6e354f63a794)
![Screenshot 2025-05-07 113535](https://github.com/user-attachments/assets/ad6b4715-13f7-4797-aa31-cf8b01a5d3ee)
