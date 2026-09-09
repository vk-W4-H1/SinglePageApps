# Universal SHA-256 Hasher

<iframe src="../sha256hashgen.html" width="100%" height="600px" style="border:none;"></iframe>

## HashStudio User Guide
HashStudio is a lightweight web tool designed to calculate the SHA-256 hash of your local files directly in your web browser.  


### How to Calculate a File Hash
#### Open the Page: 
Open sha256hashgen.html in any modern desktop or tablet web browser.  


#### Select Your File:

- **Drag and Drop:** Drag your file from your device and drop it into the drop zone box.  
HTML

- **Browse File:** Click the Select File button to choose a file using your device's file picker.  


#### View the Results:

- Once processing completes, your results will automatically display below the input box.  


- **File Info:** Shows the file name and its converted size (e.g., KB, MB).  


- **SHA-256 Hash:** Displays the 64-character hash string. Click or highlight the hash to select and copy it.  


- **Time Taken:** Displays how long the hashing process took in seconds.  


- ** Engine Badge:** Indicates whether your browser used hardware-accelerated Native Web Crypto or the JS Fallback engine.  


### Troubleshooting & Tips
- **Privacy & Security:** Files are processed entirely on your local device and are never uploaded to an external server.  


- **Performance:** Processing speed depends on your file size and device hardware. For optimal speed on large files, use a browser running in an HTTPS or secure local context to enable native hardware acceleration.  

-------------------

## Device compatibility Tested
### 1) Desktop 

| Browser | version | Max File Size | File Types |  
| ------ | ------ | ------ | ------ |  
| chrome | 150.x | 1.4 GB | .tar, .mp4, .png|
| edge | 150.x  | 1.4 GB | |
| firefox | 151.x  | 1.4 GB|.tar, .mp4, .png |

### 2) Android

|Android Version | Browser | Max File Size | File Types|
| ------ | ------ | ------ | ------ |
|14 | chrome |  150.x | | | 


## Performance Test

|File Size | Time Taken during testing |
| ---- | ----|
|1.4 GB| 15 seconds|
| 106.03 MB | 1.03 seconds|
|3.09 MB| 0.04 seconds|