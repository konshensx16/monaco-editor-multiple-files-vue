# monaco-editor

### Important thing i had to do

I had to change one of the nodes_modules files (node_modules/vue-monaco-editor/src/Monaco.vue) and change the line inside the __editorHasLoaded__ function, the code within contains an emit of an event, this event sends another param with is the editor, i had to add the monaco instance as well so i can access the models and change them.
