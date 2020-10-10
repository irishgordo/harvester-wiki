# Harvester test cases

## Virtual Machines
 ### 1. Create kubevirt.io.virtualmachine
   - [ ] Create a VM without selecting anything on the page, check the page output
   - [ ] Create a VM by selecting one of the images on the page, check the page output
   - [ ] Check the search functionality by typing in vary content, like something that there is no match in the list or something capitalized or not
   - [ ] Choose a size, check if the textboxes are allowed to type in a non-numeric value
   - [ ] Check the "Use an existing VM Template", select one of the options in the dropdown
   - [ ] Check the "Use an existing VM Template", select one of the options in the dropdown
   - [ ] Add a disk with default configurations, a new disk can be added successfully
   - [ ] Add a disk, replace the size of 100000000 or a number which is bigger, save it to see what will happen
   - [ ] Add a disk, replace the size value of 100000000 or a number which is bigger, save it to see what will happen
   - [ ] Add a disk, replace the name of a long string(can be super long), save it to see what will happen
   - [ ] Add a disk, change all the values to make sure that they are not the default ones, they can be saved successfully
   - [ ] Modify a disk, change all the values to make sure that they are not the old ones, they can be saved successfully
   - [ ] Delete a disk, it can be deleted successfully
   - [ ] Add a networking interface with default configurations, a new record can be added successfully
   - [ ] Add a networking interface, replace the name and Network Name of a long string(can be super long), save it to see what will happen
   - [ ] Add a networking interface, type in a valid/invalid Mac address, save it to see what will happen
   - [ ] Add a networking interface, change all the values to make sure that they are not the default ones, save successfully
   - [ ] Modify a networking interface, change all the values to make sure that they are not the old ones, save successfully
   - [ ] Delete a networking interface, it can be deleted successfully
   - [ ] Add cloud-init, it can be saved successfully

 ### 2. Create from YAML
   - [ ] Create from YAML, save it with nothing, no VM can be created
   - [ ] Create from YAML, save it with invalid content, no VM can be created
   - [ ] Create from YAML, save it with valid content, a new VM item can be created
   - [ ] Create from YAML, once a new item has been created, check the status, its status should be running if there is nothing incorrect in YAML˜

 ### 3. VM list
   - [ ] Select one of the VM items in the list, click Download YAML button, a YAML file will be downloaded, check if the content is correct
   - [ ] Select several of the VM items in the list, click Download YAML button, a YAML file will be downloaded, check if the content is correct
   - [ ] Select one of the VM items in the list, click Delete button, a YAML file will be downloaded, check if the content is correct
   - [ ] Select several of the VM items in the list, click Download YAML button, a YAML file will be downloaded, check if the content is correct
   - [ ] Stop a running VM, it will be stopped successfully and the status will be updated to "stopped", Node and Address will be no longer available
   - [ ] Restart a running VM, it will be restarted successfully and the status will be updated to "restarting" and finally back to "running", Node and Address will be available again
   - [ ] Restart a running VM, it will be restarted successfully and the status will be updated to "restarting" and finally back to "running", Node and Address will be available again
   - [ ] Open console of a running VM, a console window will be open and it is functional
   - [ ] Open console of a running VM, a console window will be open and it is functional
   - [ ] Edit as form, check if the original content displays correctly, edit some of them and save, like adding some labels or annotations, the changes can be applied successfully
   - [ ] Edit as YAML, check if the YAML content displays correctly, edit some of them and save, the changes can be applied successfully
   - [ ] Edit as YAML, read from a file, check if the YAML content displays correctly, only if the format is correct that it can be saved and created a new VM 
   - [ ] Edit as YAML, read from a file, choose a large file, check if the file can be imported successfully, click show diff button to see if it is functional
   - [ ] Clone as form, check if the original content displays correctly, change the hostname to make it unique, it can be saved and created a new VM
   - [ ] Clone as YAML, check if the original content displays correctly, change the hostname to make it unique and remove some other useless info, it can be saved and created a new VM
   - [ ] Download YAML, a YAML file can be downloaded successfully
   - [ ] Delete a VM, it can be deleted successfully

## Images
### 1. Create harvester.cattle.io.virtualmachineimage
   - [ ] Create a VM without filling anything on the page, check the page output
   - [ ] Create a VM without filling an image URL on the page, check the page output
   - [ ] Create a VM filling a valid image URL on the page, check the page output
   - [ ] Create a VM filling an invalid image URL on the page, check the page output
   - [ ] Create a VM without filling a name on the page, check the page output
   - [ ] Create a VM filling a valid name on the page, check the page output
   - [ ] Create a VM filling an invalid name on the page, check the page output
   - [ ] Create a VM filling all the required fields, alongside descriptions, check the page output
   - [ ] Create a VM filling all the required fields, alongside labels, check the page output
   - [ ] Create a VM filling all the required fields, alongside annotations, check the page output
   - [ ] Edit as form, check if the original content displays correctly, edit some of them and save, like adding some labels or annotations, the changes can be applied successfully
   - [ ] Edit as YAML, check if the YAML content displays correctly, edit some of them and save, the changes can be applied successfully
   - [ ] Edit as YAML, read from a file, check if the YAML content displays correctly, only if the format is correct that it can be saved and created a new image
   - [ ] Edit as YAML, read from a file, choose a large file, check if the file can be imported successfully, click show diff button to see if it is functional
   - [ ] Clone as form, check if the original content displays correctly, change the image name to make it unique, it can be saved and created a new image
   - [ ] Clone as YAML, check if the original content displays correctly, change the image name to make it unique and remove some other useless info, it can be saved and created a new image
   - [ ] Download YAML, a YAML file can be downloaded successfully
   - [ ] Delete an image, it can be deleted successfully


## Key Pairs
   - [ ] Create a key pair without filling anything on the page, check the page output
   - [ ] Create a key pair without filling an image URL on the page, check the page output
   - [ ] Create a key pair filling a valid image URL on the page, check the page output

## Templates
   - [ ] Create a template without filling anything on the page, check the page output
   - [ ] Create a template filling a template name on the page but without an image, check the page output
   - [ ] Create a template filling valid a template name and image but without CPU and Memory, check the page output

## Volumes
   - [ ] Create a volume without filling anything on the page, check the page output
   - [ ] Create a volume filling a volume name on the page but without an image, check the page output
   - [ ] Create a volume filling valid a volume name and image but without CPU and Memory, check the page output

## Authentification


## Helm Installation