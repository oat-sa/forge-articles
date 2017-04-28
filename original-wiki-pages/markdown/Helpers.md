Helpers
=======

{{\>toc}}

Utilities
---------

Helpers contain transverse functionalities of the application or an extension. By definition a *helper* helps manage non-business operations. It could be used everywhere but shouldn’t be defined in one of the MVC layers. A common representation of the helper is the toolbox: each helper is a toolbox providing a set of utilities for a particular domain.

### TAO helpers overview

You can see in the schema below an overview of some of the TAO meta-extension helpers. These helpers are independent classes, they use mainly static methods and attest to the toolbox concept.\
For instance, the *tao\_helpers\_File* helper provides some convenience methods to manipulate files.

![](http://forge.taotesting.com/attachments/391/tao-helpers.png)

Unlike the previous helpers, the class diagram below outlines a more complex helper. The purpose of these helpers is the data transfer. They supply you with a generic *Adapter* that defines a public interface for the data transfer and there is also an implementation for the mail transfer. As you can see, these classes are located in a sub-package.

![](hhttp://forge.taotesting.com/attachments/392/tao-helpers-trsf.png)

**The Uri helper**

This helper deserves your attention because you will need it or see it a lot. It’s a toolbox to manage URIs and URLs.\
We use it to write MVC URLs for you and to encode/decode the RDFS resources URIs.

-   **MVC Urls** : the method *tao\_helpers\_Uri::url* (or its convenient short-cut \_*url*, ) builds the URL for you instead of writing it manually.\
    You can give the action, module and/or extension’s name in parameters and then, this method renders the right URL.

For example, the method `_url('index')` will return to the index action of the current module.\
The following code sample will display “http://yourdomain.tld/taoItems/PreviewApi/runner?match=client&context=false”\
<code class="php">\
 echo \_url(‘runner’, ‘PreviewApi’, ‘taoItems’, array(‘match’ =\> ‘client’, ‘context’ =\> false));\
</code>

> Please note that to access web resources you have to use the method *tao\_helpers\_Uri::getBaseUrl()* (or *tao\_helpers\_Uri::getRootUrl()* if the resource is located in another extension). For example, to access an image source:

     $baseUrl = tao_helpers_Uri::getBaseUrl();
     $imageSource = $baseUrl . 'img/logo.png';

> *In the templates you can use directly the BASE\_WWW or ROOT\_URL constants for or better code readability*

![](http://forge.taotesting.com/attachments/download/215/returnTopArrow.JPG)[[Helpers|Return to Top]]

the Form Generator Engine
-------------------------

One of the helper has grown up and became a complete library. The purpose of this library is to build and manage forms dynamically. It provides a convenient way to manipulate forms as object entities from the server side. It outlines a generic model to represent forms and to build them for a particular rendering. For instance, we have implemented the XHTML rendering mode. In other words, you can instantiate objects from your PHP codes that will generate a full XHTML code.\
The Form Engine is mainly used in TAO as a front-end for the data model through the Generis API. So, it enables us to easily edit a RDF resource: the form is built regarding the resources information.

> A kind of possible contribution would be to extend the engine to render more widgets or to implement other rendering: XUL, JS forms, etc.

### Form Engine architecture overview

![](http://forge.taotesting.com/attachments/389/form-engine-model.png)

-   The *tao\_helpers\_form\_FormFactory* class enables you to retrieve initialized instances of *tao\_helpers\_form\_Forms*, *tao\_helpers\_form\_FormElements* and *tao\_helpers\_form\_Validators* classes for a defined rendering mode (the XHTML is the default rendering). For example, you can get an instance by using\
    <code class='php'>FormFactory::getForm(‘formName’);</code>
-   The *tao\_helpers\_form\_GenerisFormFactory* class gives you some utilities to generate forms from an RDFS model using the Generis API. For example, the method *map* returns the map between the widgets (from the Widget ontology) and the *tao\_helpers\_form\_FormElements* tied to a widget.

<!-- -->

-   The *tao\_helpers\_form\_Form* class is an abstract entity. It represents an entire Form. It contains a list of *tao\_helpers\_form\_FormElement* classes (the elements can be organized into groups). This class must be extended by a class for a specific render mode. The default inherited class is the *tao\_helpers\_form\_xhtml\_Form* class which provides an implementation of the *tao\_helpers\_form\_Form* rendered in XHTML.
-   The *tao\_helpers\_form\_FormElement* class is also an abstract entity. It represents a form element, a form field. It must be extended by classes for each specific field type (*tao\_helpers\_form\_elements\_TextBox*, *tao\_helpers\_form\_elements\_ComboBox*, etc.) and a rendering mode (*tao\_helpers\_form\_elements\_xhtml\_Checkbox*)
-   The *tao\_helpers\_form\_Validator* class is an abstract class that represents a validation rule. A *tao\_helpers\_form\_FormElement* instance has the ability to embed a chain of *tao\_helpers\_form\_Validator*. The *tao\_helpers\_form\_FormElement*’s *tao\_helpers\_form\_Validator* are executed during the form validation step. Each rule can break the chain and return an error message. For example, the *tao\_helpers\_form\_validators\_Rexgex* Validator class will check if the current value of a FormElement matches a regular expression.
-   The *tao\_helpers\_form\_Decorator* interface provides you with methods to decorate the rendering of the form components (elements, groups, forms and errors). The decorators use a wrapping process to add some decoration elements before and after the component. For instance, in XHTML we use he *tao\_helpers\_form\_xhtml\_TagWrapper* class. It implements the *Decorator* interface to enclose the rendering of components into an XHTML tag: the *TagWrapper::preRender* method creates the open tag and the *tao\_helpers\_form\_xhtml\_TagWrapper::postRender* method add the closing tag.
-   The *tao\_helpers\_form\_FormContainer* is a model of class to embed the form creation. The purpose of this class is to prevent the form creation inside the Controllers or the Models layer. This practice needs a consequent number of code’s lines to create a form and it will reduce readability. That’s the reason why the *tao\_helpers\_form\_FormContainer* provides an abstract model to encapsulate the form creation.

### Form Elements

The Form Elements have been structured hierarchically. The *tao\_helpers\_form\_FormElement* is the abstract class with methods shared by all the *tao\_helpers\_form\_FormElements*. Then each kind of element will be created into another abstract. And those 2nd abstract classes will be inherited by classes regarding the rendering mode.

The diagram below outlines this structure. It represents only a part of the *tao\_helpers\_form\_FormElements*. There is more available.

![](http://forge.taotesting.com/attachments/388/form-elements.png)

One of the main methods of implementation is the *rendering* method. It will produce the output to be displayed.\
Look at the following piece of code that will render an HTML text field:

     $textField = new tao_helpers_form_elements_xhtml_Textbox('firstname');
     $textField->setDescription('Enter your firstname');
     echo $textField->render();

    HTML Output:

    Enter your firstname

### Tutorial: the Form Container model

The *TAO\_helpers\_form\_Form Container* provides you with a convenient solution to add forms into your application. This is only a container in which you will put your instructions to create the forms and the form elements.\
In this tutorial, we will show you how to create a simple form, a login form using the *TAO\_helpers\_form\_Form Container* architecture.

We assume that you have a valid extension in which you can make your tests. We will refer to this extensions with the name *myExt*

**1. Creation of the container**

1.  Create a class called *myExt\_actions\_form\_Login* in the folder *myExt/actions/form*
2.  This class extends the *tao\_helpers\_form\_FormContainer* class
3.  Override the abstract methods: *initForm* and *initElements*

![](http://forge.taotesting.com/attachments/390/login_formcontainer.png)

-   In the *initForm* method, you need to create and initialize *tao\_helpers\_form\_Form* object. The *form* attribute have to refer to it.
-   In the *initElements* method, you need to create the *tao\_helpers\_form\_FormElement* objects and bind them to the *form* attribute.
-   In the listing below, we use the *tao\_helpers\_form\_FormFactory* for creations. We create a form, with a *Connect* action and we bind it to 2 fields: a login and a password field:

<!-- -->

    class myExt_actions_form_Login extends tao_helpers_form_FormContainer {

        public function initForm() {
         $this->form = tao_helpers_form_FormFactory::getForm('login');

         $connectElt = tao_helpers_form_FormFactory::getElement('connect', 'Submit');
         $connectElt->setValue(__('Connect'));
         $this->form->setActions(array($connectElt), 'bottom');
        }

        public function initElements() {
         $loginElt = tao_helpers_form_FormFactory::getElement('login', 'Textbox');
         $loginElt->setDescription(__('Login'));
         $loginElt->addValidator(tao_helpers_form_FormFactory::getValidator('NotEmpty'));
         $this->form->addElement($loginElt);

         $passElt = tao_helpers_form_FormFactory::getElement('password', 'Hiddenbox');
         $passElt->setDescription(__('Password'));
         $passElt->addValidator(
                    tao_helpers_form_FormFactory::getValidator('NotEmpty')
         );
         $this->form->addElement($passElt);
        }
    }

The container class is now finished. It contains the reference in our new form.

**2. Use of the container in a controller’s action**

1.  Create (if it doesn’t exist) a module *myExt\_actions\_Main* in your extension: *myExt/actions/class.Main.php*
2.  Create a new action *login* in the *Main* module (the URL to call it will be */myExt/Main/login*)

> One action is sufficient as the form will post on itself.

In the *login* action:

-   Instantiate your new container
-   Get the form reference thought the `tao_helpers_form_FormContainer::getForm` method.
-   Display the form: *tao\_helpers\_form\_Form::render*
-   Check if the form has been submitted: *tao\_helpers\_form\_Form::isSubmited*
-   Check if the form is valid: *tao\_helpers\_form\_Form::isValid*
-   Retrieve the posted data: *tao\_helpers\_form\_Form::getValues* and compare the login and password, then display if they are correct (using a hard coded value, for instance)

You can see an example in the listing below:

    class myExt_actions_Main extends tao_actions_CommonModule {

        public function login() {

         //instantaite our container
         $loginContainer = new myExt_actions_form_Login();

         //get the form reference 
         $loginForm = $loginContainer->getForm();

         if($loginForm->isSubmited() && $loginForm->isValid()){
           //we compare the values once posted  ( it's only to illustrate, so never do that kind of authentication!)
           if($loginForm->getValue('login') == 'admin' && md5($loginForm->getValue('password')) == md5('admin')){
               echo "Correct!";
           }
           else{
               echo "Wrong login pass!";
           }
         }

          echo $loginForm->render(); //we don't use templates for the example
        }

    }

![](http://forge.taotesting.com/attachments/download/215/returnTopArrow.JPG)[[Helpers|Return to Top]]

