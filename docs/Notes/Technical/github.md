# Github

# Cloning Repository with SSO Authentication Requirements

Using HTTPS to clone in Visual Studio does not correctly work when there are saml sso permission requirements on the GitHub repository.

An alternative is generating ssh keys. To do so, follow the steps below:

1. Generate new SSH Key </br>
Type "ssh-keygen" in terminal. </br>
```
ssh-keygen
```
Then either pick a location for the key or press enter for default. </br>
Next, enter a passcode. You must do this due to the sso restrictions.

2. Now open the ssh folder within File Explorer and open the config file. </br>
Add the following information:
```
Host github.com
HostName github.com
 User git
 IdentityFile ~/.ssh/id_rsa
```

3. The next step is to add the key to github. </br>
Type "cat ~/.ssh/id_rsa.pub" in the terminal. </br>
```
cat ~/.ssh/id_rsa.pub
```
Copy the output </br>
Now go to Github.com -> Settings -> SSH and GPG keys. </br>
Click "New SSH key". </br>
Then add a title and paste the output into the "Key" spot.

4. Next, copy the SSH information from the desired repository. </br>
Type "git clone "enter copied material"" into the terminal.

You should then be able to open the repository remotely.