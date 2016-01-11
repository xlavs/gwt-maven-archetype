> This is a Java application template to bootstrap Your GWT 2.7.0 project using Maven build and Maven archetype generation tools. It is based on the code generated by GWT `webAppCreator` but altered and documented so that You can import this project in the IntelliJ, run tests and later deploy on Tomcat server. Template source is avaliable for customisation so that You can create and use it as Your own custom maven archetype.

### Quick installation

- Run `generate-archetype.cmd` from the project root.
- Run `install-archetype.cmd` to install the generated archetype in Your local maven repository.
- To create a new project, go to Your workspace folder and run `mvn archetype:generate -DarchetypeCatalog=local`. Then select `gwt-basic-app` archetype.

### Template description

According the GWT documentation You should create a new GWT-maven project using `webAppCreator`, but it has it's flaws. 

Here is the sample command You should use according the GWT documentation:

`webAppCreator.cmd -out gwt27-basic -templates sample,maven,eclipse,readme,_sample-test com.klavs.archetypes.TemplateApp` or
`webAppCreator.cmd -out gwt27-basic -templates sample,maven,eclipse,readme,_sample-test,_eclipse-test com.klavs.archetypes.TemplateApp` if You need test integration for Eclipse.

Following changes have been made to the original template, generated by webAppCreator. These are done to convert it to the working Maven project with a capability to generate a template archetype for later use:

- Removed maven `dependencyManagement` from pom.xml and replaced it with generic artifact variables in the pom file. (KISS. Dependency management was not working and I did not want to spend time to fix that.)
- Project directory structure (sources, tests, bould target, `webapp`) is documented in the pom.xml now. This helps at least IntelliJ IDE to properly import the project as maven project, find the project tests, run them and get results.
- The servlet code resides now in its own package and the change is reflected in the `pom.xml gwt-maven-plugin` configuration. (I had to make multiple servlets in my own project so I felt this was necessary)
- A servlet, named `greetservlet` path configuration setting is added in it's `GreetServlet.gwt.xml`. Without this setting RPC calls will not work when You deploy Your application on Tomcat.
- A few empty classes are added in the project structure, because maven archetype generator was messing up the project structure without them. You should probably remove `Global.java` and `Servlet.java` when project is generated.
- A `build.cmd` has build command sample that You should use to generate a resulting applet. (I have done some testing to ensure it works on Tomcat, but I have not tested it on AppEngine yet)
- A sample gitignore sampe (`gitignore.sample`) is added with a few defaults.
- A source for the main/client application refactored a bit because inner event handler class declaration was causing IntelliJ to mess up the source when doing source formating.
- A few other changes I did while battling maven archetype generation issues.

### How to use this template to bootstrap Your own GWT project.

This GWT template is composed so that You can generate Your own maven archetype and use it for Your own project without `webAppCreator`.

#### To generate and install an archetype

- The project source resides in `source-project`
- A maven archetype is generated by `mvn archetype:create-from-project -Darchetype.properties=../archetype.properties` command from the project source.
- You can change a name, version and archetype groupId in `archetype.properties` file.
- It is highly recomended that You run `mvn clean` before You generate an archetype. 
- Also remove any files generated by IDE or other tools, because they will be picked up by archetype generator and included if You do not delete them. You might not want that. A sample "clean-source.cmd" file is availabe.
- Go to the `source-project/target/generated-sources/archetype` and run `mvn install` to install the generated archetype in Your local maven repository (Usually it is Your $HOME/.m2/.. Look for the log messages to find an exact location)

#### To create a new project from the archetype

- To create a new project from Your archetype You now use `mvn archetype:generate -DarchetypeCatalog=local` command
- To work with generated sources from the IntelliJ IDE You just import the project as maven project and let IntelliJ detect GWT framework and other project settings.

One thing should be kept in mind while maintaining a prototype for the archetype, if You decide to customize it. - The archetype generator 
will strip out all emtpy packages (and mess up Your source structure) from Your source tree if there are no Java source. 
And it will not recognize GWT `.gwt.xml` files as valid package content also. To fix that issue You have to keep at least one, valid, 
java class in each source package. It can be a simple, empty java class.

### Development process

There are a few nice GWT tutorials available on the internet. Details below describe things that are different for this setup or something I do not remember being covered in other tutorials.

- To start development You run the application by `mvn gwt:run`. In the IntelliJ You can add maven run configuration to start and stop GWT DevMode from IDE. Any change You make on the client side will trigger the DevMode to recompile and You can see the changes in the browser on the fly.
- DevMode will not manage changes You do to the server side code or any changes in the `webapp` folder. To re-compile these You should stop devmode, run `mvn clean` and `mvn gwt:run` again.
- You should monitor Your system `temp` folder, because every time gwt devmode runs it will create some temporary files there and they can pile up and cause some trouble later if You do not delete them.
- You configure Your initial landing page in `pom.xml runTarget` setting for the DevMode and `webapp/WEB-INF/web.xml welcome-file` setting for the application server.
- GWT 2.7.0 with `gwt-maven-plugin` does not work with Java 1.8 source level. You can target Your build for 1.8, but Your source is limited to 1.7. (That means no lambdas and other 1.8 stuff, in Your GWT source code, sorry)

### Adding a new servlet to the project

To add new servlet / module to the project You have to make several changes:

- Create a package for the servlet. You can use IDE refactoring tools to duplicate `greetservlet` package, but make sure Your imports are pointing to it's own code.
- Add a new servlet module to the `pom.xml configuration/modules` so that DevMode can pick it up.
- Add a servlet name and mapping configuration in `webapp/WEB-INF/web.xml`. Follow the sample.
- Add the client side code (a Javascript source, compiled by GWT from Your Java sources) to Your html page in the `webapp`. A working sample for greetservlet is `<script type="text/javascript" language="javascript" src="greetservlet/greetservlet.nocache.js"></script>`

 
