# **Step-by-Step Guide: Managing EBS Volumes and Snapshots in AWS**  

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

   
7. Once created, **attach it to an instance**:  
   - Select the volume.  
   - Click **"Actions"** → **"Attach Volume"**.  
   - Select the EC2 instance.  
   - Enter a **device name** (e.g., `/dev/xvdf`).  
   - Click **"Attach Volume"**.  

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
   NAME    MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
   xvda    202:0    0   8G  0 disk /
   xvdf    202:1    0  10G  0 disk
   ```
3. Format and mount the volume:  
   ```sh
   sudo mkfs -t ext4 /dev/xvdf
   sudo mkdir /mnt/newvolume
   sudo mount /dev/xvdf /mnt/newvolume
   ```
4. Verify the volume is mounted:  
   ```sh
   df -h
   ```

---

## **Step 2: Take a Snapshot of the EBS Volume**
1. Go to **EC2 Dashboard** → **Volumes**.  
2. Select the **EBS volume** you want to back up.  
3. Click **"Actions"** → **"Create Snapshot"**.  
4. Enter a **name/description** (e.g., `MyVolumeSnapshot`).  
5. Click **"Create Snapshot"**.  
6. Go to **EC2 Dashboard** → **Snapshots** to verify the snapshot is created.  

---

## **Step 3: Create a New Volume from a Snapshot**
1. Go to **EC2 Dashboard** → **Snapshots**.  
2. Select the snapshot you created.  
3. Click **"Actions"** → **"Create Volume"**.  
4. Select the following options:  
   - **Size:** Keep the same or increase.  
   - **Volume Type:** Choose **gp3**.  
   - **Availability Zone:** Choose the same AZ as the new EC2 instance.  
5. Click **"Create Volume"**.  

---

## **Step 4: Attach the New Volume to Another Server**
1. Go to **EC2 Dashboard** → **Volumes**.  
2. Select the new volume created from the snapshot.  
3. Click **"Actions"** → **"Attach Volume"**.  
4. Choose the **new EC2 instance** where you want to attach it.  
5. Enter a **device name** (e.g., `/dev/xvdg`).  
6. Click **"Attach Volume"**.  

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
   NAME    MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
   xvda    202:0    0   8G  0 disk /
   xvdg    202:1    0  10G  0 disk
   ```
3. Mount the volume:  
   ```sh
   sudo mkdir /mnt/restoredvolume
   sudo mount /dev/xvdg /mnt/restoredvolume
   ```
4. Verify the volume contents:  
   ```sh
   ls /mnt/restoredvolume
   ```
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

This ensures **data backup, recovery, and volume migration** between instances in AWS. Let me know if you need more details.
