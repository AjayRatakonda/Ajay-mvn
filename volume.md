# **Managing EBS Volumes and Snapshots in AWS**  

1. **Attaching an EBS volume to a server**  
2. **Taking a snapshot of an EBS volume**  
3. **Creating a new volume from a snapshot**  
4. **Attaching the new volume to another server**  

---

## **Step 1: Create and Attach an EBS Volume to a Server**  

1. **Go to AWS Console** → **EC2 Dashboard**.
2. create one ec2 instance (t2.micro)  

   ![image](https://github.com/user-attachments/assets/b23f70dd-95e4-487a-8c42-bc3fc34127f1)

3. In the left panel, click **"Volumes"** under **Elastic Block Store (EBS)**.  
4. Click **"Create Volume"**.  

   ![image](https://github.com/user-attachments/assets/a172cfc8-8b5f-422c-902c-1bb047c90375)

5. Select the following options:  
   - **Size:** Enter the required size (e.g., 1 GiB).  
   - **Volume Type:** Choose **gp2** (General Purpose SSD).  
   - **Availability Zone (AZ):** Select the **same AZ**(ap-south-1b) as your EC2 instance.  
6. Click **"Create Volume"**.  

   ![image](https://github.com/user-attachments/assets/a45f34e3-ee28-42f1-a2a2-2fcd014ac99c)
   
7. Once created, **attach it to an instance**:  
   - Select the volume.  
   - Click **"Actions"** → **"Attach Volume"**.  
   - Select the EC2 instance.  
   - Enter a **device name** (e.g., `/dev/sdf`).  
   - Click **"Attach Volume"**.  

     ![image](https://github.com/user-attachments/assets/2aeebbbd-1442-4e3b-b093-71be090686c1)
 
### **Verify the Volume on the EC2 Instance**
1. Connect to the instance via SSH:  
   ```sh
   ssh -i your-key.pem ec2-user@<instance-ip>
   ```
2. Check if the volume is attached:  
   ```sh
   lsblk
   ```
   Example output:  
   ```
   ![image](https://github.com/user-attachments/assets/8cae5032-85f9-45ca-9cc8-de70cc861407)

   ```
3. Format and mount the volume:  
   ```sh
   sudo mkfs -t ext4 /dev/xvdf
   ```
   ![image](https://github.com/user-attachments/assets/9426f8f3-0564-4555-9268-f6c1a92ee8c3)

   ```sh
   sudo mkdir /mnt/newvolume
   ```
   ```sh
   sudo mount /dev/xvdf /mnt/newvolume
   ```
   
5. Verify the volume is mounted:  
   ```sh
   df -h
   ```
   ![image](https://github.com/user-attachments/assets/c42a8516-2148-4937-8c2e-2c50206407a4)

---

## **Step 2: Take a Snapshot of the EBS Volume**
1. Go to **EC2 Dashboard** → **Volumes**.  
2. Select the **EBS volume** you want to back up.  
3. Click **"Actions"** → **"Create Snapshot"**.  
4. Enter a **name/description** (e.g., `my-snapshot`).  
5. Click **"Create Snapshot"**.  
6. Go to **EC2 Dashboard** → **Snapshots** to verify the snapshot is created.  

   ![image](https://github.com/user-attachments/assets/c1fb1a57-8f2b-42c7-a3f5-75105fb23ccb)

---

## **Step 3: Create a New Volume from a Snapshot**
1. Go to **EC2 Dashboard** → **Snapshots**.  
2. Select the snapshot you created.  
3. Click **"Actions"** → **"Create Volume"**.  
4. Select the following options:  
   - **Size:** Keep the same or increase.  
   - **Volume Type:** Choose **gp2**.  
   - **Availability Zone:** Choose the same AZ as the new EC2 instance.  
5. Click **"Create Volume"**.  

   ![image](https://github.com/user-attachments/assets/b1dc93e8-5f45-4772-aa31-e4637b25ffe4)
   ![image](https://github.com/user-attachments/assets/bba91f71-ad73-4724-bd3e-33e2b7223cf3)

---

## **Step 4: Attach the New Volume to Another Server**
1. Go to **EC2 Dashboard** → **Volumes**.  
2. Select the new volume created from the snapshot.  
3. Click **"Actions"** → **"Attach Volume"**.  
4. Choose the **new EC2 instance** where you want to attach it.  
5. Enter a **device name** (e.g., `/dev/xvdg`).  
6. Click **"Attach Volume"**.  

   ![image](https://github.com/user-attachments/assets/9d10df02-79a3-4dfc-8704-dc926310a05e)
   ![image](https://github.com/user-attachments/assets/1e057136-4b95-4399-b0b8-a0d15110be99)
   ![image](https://github.com/user-attachments/assets/2f9ae51d-24ad-4770-8d43-e0d153ceb537)
   ![image](https://github.com/user-attachments/assets/f784a3c0-f08f-46fa-9c2f-dfbc336887d8)


### **Verify and Mount the New Volume on the New Server**
1. Connect to the new instance via SSH:  
   ```sh
   ssh -i your-key.pem ec2-user@<new-instance-ip>
   ```
2. Check if the volume is detected:  
   ```sh
   lsblk
   ```
   Example output:  
   ```
   ![image](https://github.com/user-attachments/assets/698e9258-88cc-4412-a919-de67e7b6bebc)

   ```
3. Mount the volume:  
   ```sh
   sudo mkdir /mnt/restoredvolume
   sudo mount /dev/xvdf /mnt/restoredvolume
   ```
4. Verify the volume contents:  
   ```sh
   ls /mnt/restoredvolume
   ```
   If only lost+found appears, the snapshot may have been empty.

   ![image](https://github.com/user-attachments/assets/14a3f499-e8e3-4f49-94b3-6072cd2b57a2)

   If the original data is intact, the snapshot restoration is successful.

---

## **Summary**
| **Task** | **Action** |
|----------|-----------|
| **Attach EBS Volume** | Create a volume and attach it to an instance. |
| **Format & Mount** | Use `mkfs` and `mount` to make the volume usable. |
| **Take Snapshot** | Create a snapshot from an EBS volume. |
| **Restore from Snapshot** | Create a new volume from the snapshot. |
| **Attach to Another Server** | Attach the new volume and mount it on another EC2 instance. |

---
