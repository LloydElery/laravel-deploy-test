# Deploy a new App

1. **Skapa ett Vercel konto (Hobby)**

    1. Koppla kontot med GitHub (För att se repos, och 2fAuth)

        -> Authorize 

2. **Skapa ett konto på Aiven.io**
    1. Koppla kontot till GitHub
    2. Välj service 
    
        -> Service -> MySQL -> Free plan
    
        -> Namnge din db -> 'Connect'

3. **Skapa ett repository på github (måste vara public)**
    1. Skapa en mapp för projektet
    2. Lägg till en '.devcontainer' från ett tidigare projekt

    ![insert_devcontainer](img/image-1.png)

    3. Öppna terminalen och skapa en koppling till ditt repo:

        -> se till att du är i rätt mapp

    ![project-folder](img/image.png)

    ```bash
    git init
    git add .
    git commit -m "First commit"
    git branch -M master
    git remote add origin https://github.com/LloydElery/laravel-deploy-test.git
    git push -u origin master
    ```

4. **Starta docker desktop**

    1. Se till att ingen container är igång
    2. Öppna projektet i VSCode genom terminalen:

        -> Skriv följande i projektets root-mapp från terminalen

    ```bash
    code .
    ```

    3. 'Reopen in container'

        -> Klicka på knappen som poppar upp

    ![reopen_in_container](img/image.png)

    4. Se till att din container med rätt namn kör
        
        -> namnet matcher din root-mapp

    ![docker-desktop](img/image-1.png)

5. **Skapa ett Laravel-projekt**

    1. Öppna en terminal i VSCode och hoppa in i rätt mapp

    ```bash
    ls
    cd exempel-namn
    ```

    2. I VSCode terminalen skriver du:

    ```bash
    composer create-project laravel/laravel exempel-namn
    ```

    3. Hoppa in i ditt nyskapade projekt.

    ```bash
    cd exempel-namn
    ```

    4. Låt projektet ladda till 100% och vänta tills att du ser det här:

    ![project-made](img/image-2.png)

6. **Skapa en databas från VSCode terminalen**

    1. Testa så att allt fungerar:

    ```bash
    cd exempel-namn
    php artisan serve
    ```

    ![laravel-project-page](img/image-3.png)

    2. Ändra information i .env filen
    
    från:

    ![db-information-env](img/image-5.png)

    till:

    ![new-db-information](img/image-7.png)

    Använd den här informationen på avien.io

    ![avien-io-information](img/image-6.png)

    3. Skapa databas:

    ```bash
    php artisan migrate
    ```

    ![migrate-1](img/image-8.png)

    4. Logga in på databas och kolla så att kopplingen fungerar

        -> Använd informationen från avien och din .env fil

    ![db-login](img/image-9.png)

    -> server = `host`:`port`

    -> Username = `avnadmin`

    -> Password = `**************` <- ditt unika password

    -> Database = `defultdb`

    5. Nu kan du slutföra installationen på avien.io genom att klicka på 'next step' utan att fylla i information om ip och annat. Tills du kommer till:

    ![finish-button](img/image-13.png) 

    -> klicka på knappen

7. **Skapa en 'api' mapp i projekt-root-mappen**

    ![api-folder-creation](img/image-10.png)

    1. skapa en 'index.php' fil

    ![index-file-creation](img/image-11.png)

    2. Klistra in följande kod i `index.php` filen

    ```php
    <?php
    // Forward Vercel requests to normal index.php
    require __DIR__ . '/../public/index.php';
    ```

8. **Skapa en varcel.json fil i projekt-root-mappen**

    ![varcel-json](img/image-12.png)

    -> Denna fil berättar hur vår applikation ser ut och hur dess innehåll ska presenteras

    1. Klistra in följande json kod i din fil

    ```json
        {
        "version": 2,
        "framework": null,
        "functions": {
            "api/index.php": {
                "runtime": "vercel-php@0.6.1"
            }
        },
        "routes": [
            {
                "src": "/(.*)",
                "dest": "/api/index.php"
            }
        ],
        "env": {
            "APP_ENV": "production",
            "APP_DEBUG": "true",
            "APP_URL": "https://yourproductionurl.com",
            "APP_CONFIG_CACHE": "/tmp/config.php",
            "APP_EVENTS_CACHE": "/tmp/events.php",
            "APP_PACKAGES_CACHE": "/tmp/packages.php",
            "APP_ROUTES_CACHE": "/tmp/routes.php",
            "APP_SERVICES_CACHE": "/tmp/services.php",
            "VIEW_COMPILED_PATH": "/tmp",
            "CACHE_DRIVER": "array",
            "LOG_CHANNEL": "stderr",
            "SESSION_DRIVER": "cookie"
        }
    }
    ```

9. **Pusha dina ändringar**

  ```bash
  git add .
  git commit -m "My first deployment"
  git push
  ```

10. **"Import Project"**

    ![import-project](img/image-14.png)  

    1. Klicka import

    2. Säkerställ att informationen stämmer

        -> Project Name: 'exempel-namn'

        -> Framework Preset: 'Other

        -> Root Directory: (bild)

    ![app-root-directory](img/image-15.png)

    3. 'Build and Output Settings'

    ![build-and-output-settings](img/image-16.png)

    4. 'Environment Variables'

    ![environment-variables](img/image-17.png)

    Nu ska vi lägga till följande `Key` - `Value` par [Add]. Detta gör vi en och en.

    -> `APP_KEY` - `base64:dittnyckelvärde=` <- denna nyckel är unik, kopiera inte denna.

    ![alt text](img/image-19.png)

    Har du inte något värde på `APP_KEY` i din .env fil så kan du generera en:

    ```bash
    php artisan key:generate
    ```

    -> `DB_CONNECTION` - `mysql`
    
    -> `DB_HOST` - `lloydelery-db1-mysql-lloydelery-db1.a.aivencloud.com` <- Din avian 'host' string 

    -> `DB_POST` - `19256` <- Avian port value

    -> `DB_DATABASE` - `defaultdb` <- Avian database name

    -> `DB_USERNAME` - `avnadmin` <- Avian username

    -> `DB_PASSWORD` - `*************` <- Avian password

    ![result-environment-variables](img/image-20.png)

    5. Klicka "Deploy"

    ![deployment-success](img/image-21.png)

11. **Lägg till en funktion som hjälper med säkerhet och skapar https requests istället för http**

    -> `app` -> `providers` -> `AppServiceProvider.php` ->

```php
if($this->app->environment('production')) {
    URL::forceScheme('https');
}  
```

12. **Pusha dina ändringar om du har några**

    ```bash
    git add .
    git commit -m "Ready to deploy"
    git push
    ```

13. **Se till att ditt projekt är live och går att nå via länken**

    -> Efter att du har klickat 'deploy' på *vercel.com*

    ![deploy-website-1](img/image-22.png)

    -> Klicka på deplyment länken 

    ![deployment-website-2](img/image-23.png)

    -> Klicka på url:en eller 'Visit'

    ![deployment-website-3](img/image-24.png)