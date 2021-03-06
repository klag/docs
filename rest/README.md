# Rest Services
This section is devoted in illustrating how to create REST services with Jolie. Differently from standard Jolie services which are agnostic to protocols, in a REST approach we must take into account how the underlying HTTP protocol works. In a REST service indeed, only the four basic http methods can be used for defining actions on a service, they are: GET, POST, PUT and DELETE. The consequence of such a strong limitation on the possible actions to be used, is that the resulting programming style must provide expressiveness on data instead of verbs. Such a characteristic has the main consequence to focus the programming style to the resources: we are not free to program all the actions we would like, but we are free to program all the resources we would like.

## A Jolie router for mapping rest methods into Jolie operations
The main idea behind the creation of a REST service with Jolie is the introduction of a specific http router between the caller and the Jolie service to expose as a REST service. The http router is in charge to convert all the rest calls into the corresponding Jolie operations.

![](../.gitbook/assets/rest.png)
