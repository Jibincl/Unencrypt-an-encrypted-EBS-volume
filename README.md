# Unencrypt-an-encrypted-EBS-volume
How to unencrypt an already encrypted EBS volume

## Description

Hello,
We would able to encrypt an EBS volume in the stage of creation. Encrypting would enable an extra safty to the volume. If we lost the key used for the encryption, it would be more difficult to keep the instance. So ,here im going to mention about how can we unencrypt an encrypted EBS volume.

## Table of Contents
1. Stop the running instance which the encrypted volume is attached with.
2. Dettach the encrypted volume from it
3. Create a new instance and a new volume without encryption
4. Attach both volumes to the new instance.
5. Copy the contents from encrypted volme to new one.
6. dettach the new volume from instance.
7. Attach the new volume to old instnace.
8. Start the instance

Now, let’s get into it!

## Step 1 : Stop the running instance which the encrypted volume is attached with.


![image](https://user-images.githubusercontent.com/100774483/158525358-b4277740-cd45-46aa-88c6-6268e6dd2bfd.png)

![image](https://user-images.githubusercontent.com/100774483/158525411-6859e19b-1622-4fe2-9e0f-d5823ab0056e.png)

Stopping the instance now.

![image](https://user-images.githubusercontent.com/100774483/158525550-33678491-1dd0-43ac-9c9e-6f576d1fc105.png)

## Step 2 :  Dettach the encrypted volume from it

We can check the volume status and kms key id used under "storage"

![image](https://user-images.githubusercontent.com/100774483/158525947-968a9d54-bad0-4ec9-8634-d61a4a152685.png)


Now, proceeding to dettach the volume.

1. Click on the volume ID
2. Select the voume and proceed to "dettach volume" from "actions"

![image](https://user-images.githubusercontent.com/100774483/158526383-e9f90bf4-f8cb-4585-8ba7-2e84f9bdcd3f.png)

3. Check the current status. The volume will be in available status

![image](https://user-images.githubusercontent.com/100774483/158526518-1f53960e-0fe1-448b-baa2-e62fde60cf21.png)



## Step 3 : Create a new instance  and new volume as without encryption.

1. Go to EC2 dashboard and create a new instance.

![image](https://user-images.githubusercontent.com/100774483/158528790-643dc6c6-7225-4c50-8037-172d1ebb18da.png)


2. Create new volume without encryption

Go to volume and click on cerate volume

![image](https://user-images.githubusercontent.com/100774483/158527189-51ba692d-fd8e-4069-bf10-03c151aeda68.png)


Choose same size and availabilitu zone as the encrypted volume.

![image](https://user-images.githubusercontent.com/100774483/158527348-3dd1c36d-8eb0-4137-b0f4-f2d949edf87a.png)

Untick the encryption and provide tag then proceed to create.

![image](https://user-images.githubusercontent.com/100774483/158527550-0575af05-2807-4955-ab25-e6faed44f096.png)


Check the status od new volume.

![image](https://user-images.githubusercontent.com/100774483/158527980-be946f90-07bf-4eb3-927d-852e444d5b7b.png)



## Step 4 : Attach both volumes to the new instance

1. Select the volume and click "Attach volume" from "Actions"

![image](https://user-images.githubusercontent.com/100774483/158528398-8816f057-c2ef-4c78-8ac3-183eba9b8938.png)

2. Select the new instance for "instance" tab and proceed to attach.

![image](https://user-images.githubusercontent.com/100774483/158528947-b9fe8ac2-53f2-4def-a885-4cc5589b5e0a.png)


Do the same for other volume too.  Encrypted volume is attached as "/dev/sdf" and new volume s attached as "/dev/sdg"

The volumes will be in "in use" status.

![image](https://user-images.githubusercontent.com/100774483/158529320-1eb4c7ab-93c6-47bd-820b-ec31ddaa084b.png)


## Step 5 Copy the contents from encrypted volme to new one.

1. Login to the new instance
2. Check the added volumes

~~~
  lsblk
NAME    MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
xvda    202:0    0   8G  0 disk
└─xvda1 202:1    0   8G  0 part /
xvdf    202:80   0   8G  0 disk
└─xvdf1 202:81   0   8G  0 part
xvdg    202:96   0   8G  0 disk
~~~

3. Copy the contenst from encrypted volume to unencrypted volume by using "dd".

~~~
 dd if=/dev/xvdf of=/dev/xvdg bs=4096 status=progress
~~~

~~~
 dd if=/dev/xvdf of=/dev/xvdg bs=4096 status=progress
8574980096 bytes (8.6 GB) copied, 133.172507 s, 64.4 MB/s
2097152+0 records in
2097152+0 records out
8589934592 bytes (8.6 GB) copied, 134.369 s, 63.9 MB/s

~~~


## Step 6 : dettach the new volume from instance.

Select the new volume and proceed to "dettach" under "Actions"

![image](https://user-images.githubusercontent.com/100774483/158531311-6ad0722e-b4be-42a1-b817-ef0874e69cca.png)



## Step 7 : Attach the new volume to stopped old instnace.

1. Click "Attach" under "Actions"
2. Select the old stopped instance for "instances"

![image](https://user-images.githubusercontent.com/100774483/158531700-b68b74f2-cba6-4575-87b3-af018b067a7d.png)

3. Change the device name as "/dev/xvda" then attach the volume

![image](https://user-images.githubusercontent.com/100774483/158531939-94e5c1f1-540d-4937-8cfd-c736c564c51c.png)


## Step 8 : Sart the instance

1. Go to EC2 dashboard and start the stopped instance.

![image](https://user-images.githubusercontent.com/100774483/158532306-59d07ae9-d643-42ed-9972-fde00762aaf2.png)

2. Check the volume status under "Storage"

![image](https://user-images.githubusercontent.com/100774483/158532450-3292437f-1fc7-4704-b200-e2ddb8e81d87.png)

Also, we could seethat the new unencrypted volume is propperly attached to the instance.

~~~
 df -h
Filesystem      Size  Used Avail Use% Mounted on
devtmpfs        474M     0  474M   0% /dev
tmpfs           483M     0  483M   0% /dev/shm
tmpfs           483M  468K  483M   1% /run
tmpfs           483M     0  483M   0% /sys/fs/cgroup
/dev/xvda1      8.0G  1.6G  6.4G  21% /
tmpfs            97M     0   97M   0% /run/user/0
tmpfs            97M     0   97M   0% /run/user/1000
~~~

We can cofirm on the files are copied properly by calling the Public IPv4 DNS

![image](https://user-images.githubusercontent.com/100774483/158535166-1cbe0280-755d-4705-b31e-bc7a43af638e.png)


# Conclusion

In this tutorial we discussed how to unencrypt an encrypted volume. The goal is to get you started on using this methode if we dont have the key as it is cheap and easy to do.
