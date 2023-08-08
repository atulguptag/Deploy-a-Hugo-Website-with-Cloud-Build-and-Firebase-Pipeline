## Lab Name - Deploy a Hugo Website with Cloud Build and Firebase Pipeline [GSP747]

## Lab Link - [CLICK HERE](https://www.cloudskillsboost.google/focuses/14353?parent=catalog)

## Task 1. Manual deployment

* Goto *Navigation Menu* > *Compute Engine* > Click on the SSH of your instance.

Run the following commands in the SSH of VM Instance.

```cmd
cd ~
/tmp/installhugo.sh
sudo apt-get update
sudo apt-get install git

cd ~
gcloud source repos create my_hugo_site
gcloud source repos clone my_hugo_site

cd ~
/tmp/hugo new site my_hugo_site --force

cd ~/my_hugo_site
git clone \
  https://github.com/budparr/gohugo-theme-ananke.git \
  themes/ananke
echo 'theme = "ananke"' >> config.toml

sudo rm -r themes/ananke/.git
sudo rm themes/ananke/.gitignore 

cd ~/my_hugo_site
/tmp/hugo server -D --bind 0.0.0.0 --port 8080
```
### *Note: Make sure to open a browser tab and browse to the external IP address at port 8080. Use the following URL, replacing [EXTERNAL IP] with the external IP address of your instance:*

```cmd
http://[EXTERNAL IP]:8080
```
## *Now, press `Ctrl+C`, after you complete your above task.*

### Deploy the site to Firebase

```cmd
curl -sL https://firebase.tools | bash

cd ~/my_hugo_site
firebase init

/tmp/hugo && firebase deploy
```

## Task 2. Automate the deployment

```cmd
git config --global user.name "hugo"
git config --global user.email "hugo@blogger.com"

cd ~/my_hugo_site
echo "resources" >> .gitignore

git add .
git commit -m "Add app to Cloud Source Repositories"
git push -u origin master

cd ~/my_hugo_site
cp /tmp/cloudbuild.yaml .

cat cloudbuild.yaml

gcloud alpha builds triggers import --source=/tmp/trigger.yaml

cd ~/my_hugo_site

nano config.toml #Edit the title of this file. Like this - "Blogging with Hugo and Cloud Build"
```

```cmd
git add .
git commit -m "I updated the site title"
git push -u origin master

gcloud builds list
```

### Check the build logs for the current build:

```cmd
gcloud builds log $(gcloud builds list --format='value(ID)' --filter=$(git rev-parse HEAD))
```

### Grab the URL from the build performed:

```cmd
gcloud builds log $(gcloud builds list --format='value(ID)' --filter=$(git rev-parse HEAD)) | grep "Hosting URL"
```
