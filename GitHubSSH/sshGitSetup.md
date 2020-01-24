# Setting SSH Keys to work with GitHub
Set up your SSH key so that comitting gets easier! If you want the instructions in Portuguese but in [txt format](https://github.com/lar-deeufba/tools/blob/master/GitHubSSH/sshGitSetup.txt)

1. Creating the SSH keys on Ubuntu:

	``` $ ssh-keygen -t rsa -b 4096 -C "seuemail@gmail.com" ```

	[Press enter when prompted and it will save your generated key on ``` ~/.ssh ```]

    [Now insert a password, everytime you commit this password will be prompted.]

2. Add the generated key (the non-public one) to our computer:

	``` $ ssh-add ~/.ssh/id_rsa ```

	[Insert your password]

3. Copy the public key into your clipboard and paste it on GitHub!

	``` $ gedit ~/.ssh/id_rsa.pub ```

	[Copy the whole key!]

    [Go to GitHub: Settings -> SSH and GPG Keys -> New SSH key -> Paste the copied key from clipboard]

    **Done!**

4. Now you may clone/commit repos using SSH: [**Click on USE SSH and copy**]

 ![ssh](https://user-images.githubusercontent.com/24254286/73037202-eb933e00-3e2c-11ea-9743-822a81420a42.png)


### Changing from HTTPS to SSH
If you have already committed to a repository of yours using HTTPS and you wish to keep committing but using SSH instead:

Go to the directory of the repository you're working:

``` $ git remote set-url origin git@github.com:USERNAME/REPONAME.git ```

Afterwards, just commit and push like you usually do. You will be prompted about saying yes or no, write "yes" and click enter.
