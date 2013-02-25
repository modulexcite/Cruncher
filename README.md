#Cruncher

###A CSS, LESS and Javascript Processor for ASP.NET

##What is is?
Cruncher is a C# NET4 library which concatenates, minifies, caches and serves CSS, LESS and JavaScript files using Micosoft's AjaxMin library.

##Why?
Partly for the purposes of learning but mostly because most alternatives out there aren't that great. Even Microsofts Bundle is a bit flakey.

Cruncher can handle unlimited combinations of remote and local CSS, LESS and JavaScript files. It combines them, minifies them and caches them in the browser. It can handle nested css @import statements and has a self cleaning cache should any changes be made to any of the resource files. It'll even gzip compress the output.

If you use Cruncher please get in touch on my twitter @james_m_south.

Feedback is always welcome.

##Installation
Installation is simple. A Nuget package is available [here][1]. 

  [1]: https://nuget.org/packages/Cruncher/

Alternatively you can download and build the project and reference the binaries. **Cruncher.dll** **dotless.dll** and **AjaxMin.dll**
then add the following to your **web.config**

    <!-- Add to start of the configuration section. -->
    <sectionGroup name="cruncher">
      <section name="security" requirePermission="false" type="Cruncher.Config.CruncherSecuritySection, Cruncher" />
      <section name="processing" requirePermission="false" type="Cruncher.Config.CruncherProcessingSection, Cruncher" />
      <section name="cache" requirePermission="false" type="Cruncher.Config.CruncherCacheSection, Cruncher" />
    </sectionGroup>
  
    <!-- Add to httphandlers in the system.web section. -->
    <httpHandlers>
      <!--The CSS compression HttpHandler-->
      <add verb="*" path="css.axd" type="Cruncher.HttpHandlers.CssHandler, Cruncher" validate="false"/>
      <!--The JavaScipt compression HttpHandler-->
      <add verb="*" path="js.axd" type="Cruncher.HttpHandlers.JavaScriptHandler, Cruncher" validate="false"/>
    </httpHandlers>
    
    <!-- Add to httphandlers in the system.webServer section. -->
    <handlers>
      <!--The CSS compression HttpHandler-->
      <add name="CssHandler" verb="*" path="css.axd" type="Cruncher.HttpHandlers.CssHandler, Cruncher" resourceType="Unspecified" requireAccess="Script" preCondition="integratedMode"/>
      <!--The JavaScipt compression HttpHandler-->
      <add name="JavaScript" verb="*" path="js.axd"  type="Cruncher.HttpHandlers.JavaScriptHandler, Cruncher" resourceType="Unspecified" requireAccess="Script" preCondition="integratedMode"/>
    </handlers>
  
    <!-- Add this to the end of the configuration section  -->
    <cruncher>
      <!-- Whether to allow remote downloads -->
      <!-- The maximum allowed remote file size in bytes -->
      <!-- The remote file download timeout in milliseconds -->
      <security allowRemoteDownloads="true" maxBytes="524288" timeout="300000">
          <!--
          A list of white-listed urls from which we are allowed to download and process remote files.
          The token value allows us to add the file reference without the risk of hitting IEs 1024 
          character url limit.
          -->
          <whiteList>
              <add token="jquery" url="http://ajax.googleapis.com/ajax/libs/jquery/1.9.1/jquery.js"/>
          </whiteList>
      </security>
      <processing>
          <!-- Whether to minify the css and js files and whether to compress the http response using gzip.-->
          <compression minifyCSS="true" minifyJS="true" compressResponse="true"/>
          <!-- The comma separated virtual paths to the css and js folders.-->
          <virtualPaths cssPaths="~/content" jsPaths="~/scripts" />
          <!-- A value used to replace the token '{root}' within a css file to determine the root path for resources. -->
          <relativeCssRoot path="/content" />
      </processing>
      <!-- The number of days to store a client resource in the cache. -->
      <cache maxDays="365"/>
    </cruncher>
  
To request your files you just need to create links and script tags as such

    <!-- Request three local css.files  -->
    <link  href="/css.axd?path=normalize.css|style.css|helpers.css|styles.less" rel="stylesheet" type="text/css" />
    
    <!-- Request an external copy of jQuery and a local copy of Modernizr  -->
    <script src="/js.axd?path=jquery|modernizr-1.7.js"></script>
    
Turning the cache and compression off is as simple as changing the **minifyCSS** and **minifyJS** properties in the configuration section in your web.config.
