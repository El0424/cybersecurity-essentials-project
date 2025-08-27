# Lab Report: Steganography Task

---

## Task Summary

### Required Actions
1. Encrypt the `John.txt` file into `send.png` and save it in the `Documents` folder.  
2. Password-protect the file with: `NoMor3L3@ks!`  
3. Confirm the functionality of the steganography process.  

---

## Explanation

### Steps to Complete the Lab

#### 1. Encrypt the user data into the file to be shared
1. In the **search field** on the taskbar, type `OpenStego`.  
2. Under **Best match**, select **OpenStego**.  
3. Select **Message**, **Cover**, and **Output Stego** files.  

#### 2. Select the files
- **Message File:**  
  1. Click the ellipses `[...]` at the end of the field.  
  2. Double-click `John.txt` to select the file.  

- **Cover File:**  
  1. Click the ellipses `[...]` at the end of the field.  
  2. Double-click `gear.png` to select the file.  

- **Output Stego File:**  
  1. Click the ellipses `[...]` at the end of the field.  
  2. Enter `send.png` in the File name field and click **Open**.  

#### 3. Password-protect the file
1. Enter `NoMor3L3@ks!` in the **Password** field.  
2. Enter `NoMor3L3@ks!` in the **Confirm Password** field.  
3. Select **Hide Data**.  
4. Click **OK**.  

---

#### 4. Extract the data
1. Under **Data Hiding**, select **Extract Data**.  
2. **Input Stego File:**  
   - Click the ellipses `[...]`  
   - Double-click `send.png`  
3. **Output Folder for Message File:**  
   - Click the ellipses `[...]`  
   - Double-click `Export` to set as the destination folder  
   - Click **Select Folder**  
4. Enter `NoMor3L3@ks!` in the **Password** field.  
5. Select **Extract Data** and click **OK**.  

---

#### 5. Verify the decryption
1. Open **File Explorer** from the taskbar.  
2. Navigate to the `Documents` folder.  
3. Navigate to the `Export` folder.  
4. Double-click `John.txt` to confirm successful decryption.  
