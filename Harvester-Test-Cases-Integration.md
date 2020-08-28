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

## Images

## Key Pairs

## Templates

## 