!SLIDE subsection
# Example sub-section

!SLIDE small incremental bullets
# `PerfectStrategy`

* Plugged into various places
* For example in `PerfectStrategyResolver`
* ...

!SLIDE smaller
# Code Sample

    @@@ java
    public class DispatcherServletInitializer extends
          AbstractAnnotationConfigDispatcherServletInitializer {

      protected Class<?>[] getRootConfigClasses() {
        return new Class<?>[] { RootConfig.class };
      }









    }

!SLIDE smaller
# Code Sample

    @@@ java
    public class DispatcherServletInitializer extends
          AbstractAnnotationConfigDispatcherServletInitializer {

      protected Class<?>[] getRootConfigClasses() {
        return new Class<?>[] { RootConfig.class };
      }

      protected Class<?>[] getServletConfigClasses() {
          return new Class<?>[] { WebMvcConfig.class };
      }





    }

!SLIDE smaller
# Code Sample

    @@@ java
    public class DispatcherServletInitializer extends
          AbstractAnnotationConfigDispatcherServletInitializer {

      protected Class<?>[] getRootConfigClasses() {
        return new Class<?>[] { RootConfig.class };
      }

      protected Class<?>[] getServletConfigClasses() {
          return new Class<?>[] { WebMvcConfig.class };
      }

      protected String[] getServletMappings() {
          return new String[] { "/" };
      }

    }

