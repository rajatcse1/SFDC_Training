# setup
1. create project
    ```
    cd path/to/your/sfdx/projects
    sfdx force:project:create --projectname MyLWC
    cd MyLWC
    ```
2. Authorize
    ```
    sfdx force:auth:web:login -d -a LWC-Hub
    ```
3. Create default scratch org
    ```
    sfdx force:org:create -s -f config/project-scratch-def.json -a "LWC"
    ```
4. Create component
    ```
    sfdx force:lightning:component:create --type lwc -n myComponent -d force-app/main/default/lwc
    ```
5. push
    ```
    sfdx force:source:push
    ```
6. Pull
    ```
    sfdx force:source:pull
    ```
7. Open
    ```
    sfdx force:org:open
    ```
