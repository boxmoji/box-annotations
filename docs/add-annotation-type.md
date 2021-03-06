# Adding support for new Annotation types
See [Image Point Annotations](https://github.com/box/box-annotations/tree/master/src/image) for a basic implementation of an annotation type.

### Abstract Classes
- Annotator: This class is the communicator between the file itself and the [controller](https://github.com/box/box-annotations/blob/master/src/controllers/AnnotationModeController.js).
- Annotation Controller: This class is the communicator between the [Annotator](https://github.com/box/box-annotations/blob/master/src/Annotator.js) and the [Annotation threads](https://github.com/box/box-annotations/blob/master/src/AnnotationThread.js). Each controller maintains an object of pages which contains an [R-tree](https://github.com/mourner/rbush)
- Annotation Thread: This class maintains a 'thread' of annotations associated by a unique thread ID, ie a group of annotations at the same location
- Annotation Dialog: This class maintains the dialog UI corresponding with an [annotation thread](https://github.com/box/box-annotations/blob/master/src/AnnotationThread.js), displaying the metadata of annotations and buttons to create/update/delete

** {**VIEWER**} refers to a viewer in [Box Content Preview](https://github.com/box/box-content-preview#viewers)

### The following files/abstract methods need to be implemented:
* controllers/{**TYPE**}ModeController.js i.e. controllers/DrawingModeController.js (extends [AnnotationModeController](https://github.com/box/box-annotations/blob/master/src/controllers/AnnotationModeController.js)):
    * Abstract methods that should be implemented by subclasses:
        * **handleThreadEvents()** - Handles annotation [thread events](thread.md#events) and emits them to the viewer
        * **enter()** - Enables the specified annotation mode
        * **exit()** - Disables the specified annotation mode

* {**VIEWER**}/{**TYPE**}Dialog.js i.e. doc/DocPointDialog.js (extends [AnnotationDialog](https://github.com/box/box-annotations/blob/master/src/AnnotationDialog.js)):
    * Abstract methods that should be implemented by subclasses:
        * **position()** - positions the dialog relative to the associated annotation thread

* {**VIEWER**}/{**TYPE**}Thread.js i.e. doc/DocPointThread.js (extends [AnnotationThread](https://github.com/box/box-annotations/blob/master/src/AnnotationThread.js)):
    * Abstract methods that should be implemented by subclasses:
        * **show()** - positions and shows the UI indicator for thread (blue icon for point annotations, highlight itself for highlights, etc)
        * **createDialog()** - creates and caches the appropriate dialog

### The following files need to be modified:
* {**VIEWER**}/{**VIEWER**}Annotator.js i.e. doc/DocAnnotator.js (extends [Annotator](https://github.com/box/box-annotations/blob/master/src/Annotator.js)):
    * Methods that should be modified
        * **getLocationFromEvent()** - Returns an annotation location on an image from the DOM event or null if no correct annotation location can be inferred from the event. For point annotations, we return the (x, y) coordinates for the point with the top left corner of the image as the origin. See [DocAnnotator.getLocationFromEvent()](https://github.com/box/box-annotations/blob/master/src/doc/DocAnnotator.js#L145) to see how to support multiple annotation types with this method
        * **createAnnotationThread()** - Creates the proper type of AnnotationThread based on the type of the newly created annotation, and returns


To test the new annotation type in the specified viewer, add the annotation type to the appropriate viewer option when instantiating BoxAnnotations. See [Enabling/Disabling Annotations and Annotation Types](docs/enablingdisabling-annotations-and-annotation-types.md) for more details.
