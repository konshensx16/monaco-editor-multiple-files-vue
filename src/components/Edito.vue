<template>
  <div style="display: flex;flex: 1 1 0%;position: relative;overflow: hidden;">
    <MonacoEditor
      language="javascript"
      :code="value"
      :editorOptions="options"
      @mounted="onMounted"
      @codeChange="onCodeChange"
      @renameFile="_renamePath"
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
      _removePath: function (path) {
        // remove the state
        this.editorStates.delete(path);

        let model = this.monaco.editor.getModels()
          .find(model => model.uri.path === path)

        model && model.dispose();
      },
      _renamePath: function ({oldPath, newPath}) {
        const selection = this.editorStates.get(oldPath);

        this.editorStates.delete(oldPath);
        this.editorStates.set(newPath, selection);

        this._removePath(oldPath);

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
          model = this.monaco.editor.createModel(
            value,
            'javascript',
            new this.monaco.Uri().with({ path })
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
    beforeUpdate() {
    },
    watch: {
      value: function (value) {
        // this code is required for the undo redo operations
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
      },
      path: function (path, prevPath) {
        this.editor.updateOptions(this.options);

        if (this.path !== prevPath) {
          this.editorStates.set(prevPath, this.editor.saveViewState());

          this._openFile(path, this.value);
        } else if (this.value !== this.editor.getModel().getValue()) {
          const model = this.editor.getModel();

          if (this.value !== model.getValue()) {
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
    },
    beforeDestroy() {
      this.editor && this.editor.dispose()
    }
  }
</script>
