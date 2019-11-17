# Chula CICD Workshop
1. Sign up in Heroku: https://signup.heroku.com/login
2. Verify your account in registered e-mail
3. Log in to your account
4. Select `New` (on the right-top) and then `Create new app`
5. Fill `App Name` e.g. `<your_name>-chula-cicd`

## How to integrate to github-actions
### Firstly, add secrets
1. Click on your avatar on the right-top corner
2. Select `Account settings`
3. Scroll down to `API Key` and `Generate API Key...`
4. Copy `API Key`
5. Go back to your github project repository
6. Select `Settings` of your github repository
7. Go to `Secrets` and `Add a new secret`
  - Name: HEROKU_API_KEY
  - Value: `<your_heroku_api_key>`
  - Name: HEROKU_EMAIL
  - Value: `<your_registered_heroku_email>`

### Secondly, add Heroku step to our yaml file
```yaml
release:
  name: Heroku
  runs-on: ubuntu-latest
  env:
    HEROKU_API_KEY: ${{ secrets.HEROKU_API_KEY }}
    HEROKU_EMAIL: ${{ secrets.HEROKU_EMAIL }}
    HEROKU_APP_NAME: "<your_heroku_app_name>-chula-cicd"
  steps:
    - name: Checkout current repository
      uses: actions/checkout@v1
    - name: Create netrc file connect to heroku
      run: |+
        cat >~/.netrc <<EOF
        machine api.heroku.com
            login $HEROKU_EMAIL
            password $HEROKU_API_KEY
        machine git.heroku.com
            login $HEROKU_EMAIL
            password $HEROKU_API_KEY
        EOF
    - name: Add heroku git remote
      run: heroku git:remote -a $(basename $HEROKU_APP_NAME)
    - name: Push current code in github to heroku
      run: git push heroku HEAD:refs/heads/master
```
Push to git
```
git add .
```
```
git commit -m "<commit message>"
```
```
git push origin master
```
