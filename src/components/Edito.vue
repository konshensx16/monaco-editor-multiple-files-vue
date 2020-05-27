<template>
  <div>
    <MonacoEditor
      height="600"
      language="javascript"
      :code="value"
      :editorOptions="options"
      @mounted="onMounted"
      @codeChange="onCodeChange"
    >
    </MonacoEditor>
  </div>

</template>

<script>
  import MonacoEditor from 'vue-monaco-editor'

  export default {
    props: {
      files: Object,
      path: String,
      value: String,
    },
    components: {MonacoEditor},
    name: 'Edito',
    data() {
      return {
        options: {
          selectOnLineNumbers: false
        },
        editorStates: new Map()
      };
    },
    methods: {
      // usually this just gets back the editor, i changed to source code to get the monaco instance as well
      // to have access to the models and mutate them
      onMounted(editor, monaco) {
        this.editor = editor;
        this.monaco = monaco;
        // initiliaze all files, meaning create a model for each one of them
        // path means current ofc in this context
        Object.keys(this.files).forEach(path => {
          this._initializeFile(path, this.files[path]);
        });
        // TODO: after that open the current file and display it

        this._openFile(this.path, this.value);
      },
      onCodeChange(editor) {
        // emit back to the parent to update the code
        this.$emit('codeChanged', editor.getValue())
      },
      _initializeFile: function (path, value) {
        // check if we have a model, if we dont' create a new one
        let model = this.monaco.editor.getModels()
        .find(model => model.uri.path === path)

        if (model) {
          // If a model exists, we need to update it's value
          // This is needed because the content for the file might have been modified externally
          // Use `pushEditOperations` instead of `setValue` or `applyEdits` to preserve undo stack
          model.pushEditOperations(
            [],
            [
              {
                range: model.getFullModelRange(),
                text: value,
              },
            ]
          );
        } else {
          model = monaco.editor.createModel(
            value,
            'javascript',
            new monaco.Uri().with({ path })
          );
          model.updateOptions({
            tabSize: 2,
            insertSpaces: true,
          });
        }
        return;
      },
      _openFile: function (path, value) {
        this._initializeFile(path, value);

        const model = this.monaco.editor
          .getModels()
          .find(model => model.uri.path === path);

        this.editor.setModel(model);

        // Restore the editor state for the file
        const editorState = this.editorStates.get(path);

        if (editorState) {
          this.editor.restoreViewState(editorState);
        }

        this.editor.focus();
      }
    },
    mounted() {

    },
    watch: {
      value: function (value) {
        // create a new model from this? but this is the same editor
        let model = this.editor.getModel();
        model.pushEditOperations(
          [],
          [
            {
              range: model.getFullModelRange(),
              text: value,
            },
          ]
        );
      }
    }
  }
</script>
