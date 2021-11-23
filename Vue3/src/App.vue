<template>
  <div>
  <div ref="de_titlebar" id="documenteditor_titlebar" class="e-de-ctn-title">
    <div v-on:keydown="titleBarKeydownEvent" v-on:click="titleBarClickEvent" class="single-line"
      id="documenteditor_title_contentEditor" title="Document Name. Click or tap to rename this document."
      contenteditable="false">
      <label v-on:blur="titleBarBlurEvent" id="documenteditor_title_name" :style="titileStyle">{{documentName}}</label>
    </div>
    <ejs-button id="de-print" :style="iconStyle" :iconCss="printIconCss" v-on:click="printBtnClick"
      title="Print this document (Ctrl+P).">Print</ejs-button>
    <ejs-dropdownbutton ref="de-export" :style="iconStyle" :items="exportItems" :iconCss="exportIconCss"
      cssClass="e-caret-hide" content="Download" v-bind:select="onExport" :open="openExportDropDown"
      title="Download this document."></ejs-dropdownbutton>
  </div>
  <ejs-documenteditorcontainer id='container' ref="doceditcontainer" :enableToolbar='true' height="590px"
    :serviceUrl='serviceUrl'></ejs-documenteditorcontainer>
</div>
</template>
<script>
import {
  DocumentEditorContainerComponent,
  Toolbar,
} from '@syncfusion/ej2-vue-documenteditor';
import { DropDownButtonComponent } from '@syncfusion/ej2-vue-splitbuttons';
import { ButtonComponent } from '@syncfusion/ej2-vue-buttons';
import '../node_modules/@syncfusion/ej2-base/styles/material.css';
import '../node_modules/@syncfusion/ej2-buttons/styles/material.css';
import '../node_modules/@syncfusion/ej2-inputs/styles/material.css';
import '../node_modules/@syncfusion/ej2-popups/styles/material.css';
import '../node_modules/@syncfusion/ej2-lists/styles/material.css';
import '../node_modules/@syncfusion/ej2-navigations/styles/material.css';
import '../node_modules/@syncfusion/ej2-splitbuttons/styles/material.css';
import '../node_modules/@syncfusion/ej2-dropdowns/styles/material.css';
import '../node_modules/@syncfusion/ej2-vue-documenteditor/styles/material.css';
import '../node_modules/@syncfusion/ej2-vue-buttons/styles/material.css';
import '../node_modules/@syncfusion/ej2-vue-splitbuttons/styles/material.css';

//Component registeration
export default {
  name: 'App',
  components: {
    // Declaring component
    'ejs-documenteditorcontainer': DocumentEditorContainerComponent,
    'ejs-button': ButtonComponent,
    'ejs-dropdownbutton': DropDownButtonComponent,
  },
  data() {
    return {
      serviceUrl:
        'https://ej2services.syncfusion.com/production/web-services/api/documenteditor/',
      documentName: 'Getting Started',
      documentTitle: 'Untitled Document',
      iconStyle:
        'float:right;background: transparent;box-shadow:none;border-color: transparent;border-radius: 2px;color:inherit;font-size:12px;text-transform:capitalize;margin-top:4px;height:28px;font-weight:400;font-family:inherit;',
      titileStyle:
        'text-transform:capitalize;font-weight:400;font-family:inherit;text-overflow:ellipsis;white-space:pre;overflow:hidden;user-select:none;cursor:text',
      printIconCss: 'e-de-icon-Print e-de-padding-right',
      exportIconCss: 'e-de-icon-Download e-de-padding-right',
      exportItems: [
        { text: 'Microsoft Word (.docx)', id: 'word' },
        { text: 'Syncfusion Document Text (.sfdt)', id: 'sfdt' },
      ],
    };
  },
  provide: {
    DocumentEditorContainer: [Toolbar],
  },
  methods: {
    onExport: function (args) {
      switch (args.item.id) {
        case 'word':
          this.save('Docx');
          break;
        case 'sfdt':
          this.save('Sfdt');
          break;
      }
    },
    openExportDropDown: function () {
      // tslint:disable-next-line:max-line-length
      document.getElementById('word').setAttribute('title','Download a copy of this document to your computer as a DOCX file.');
      // tslint:disable-next-line:max-line-length
      document.getElementById('sfdt').setAttribute('title', 'Download a copy of this document to your computer as an SFDT file.');
    },
    save: function (format) {
      // tslint:disable-next-line:max-line-length
      this.$refs.doceditcontainer.ej2Instances.documentEditor.save( this.$refs.doceditcontainer.ej2Instances.documentEditor.documentName == '' ? 'sample' :  this.$refs.doceditcontainer.ej2Instances.documentEditor.documentName, format);
    },
    openBtnClick: function () {
      this.$refs.uploadDocument.click();
    },
    printBtnClick: function () {
      this.$refs.doceditcontainer.ej2Instances.documentEditor.print();
    },
    titleBarKeydownEvent: function (e) {
      if (e.keyCode === 13) {
        e.preventDefault();
        document.getElementById("documenteditor_title_contentEditor").contentEditable = 'false';
        if (document.getElementById("documenteditor_title_contentEditor").textContent === '') {
            document.getElementById("documenteditor_title_contentEditor").textContent = 'Document1';
        }
      }
    },
    titleBarBlurEvent: function () {
      if (document.getElementById("documenteditor_title_contentEditor").textContent === '') {
        document.getElementById("documenteditor_title_contentEditor").textContent = 'Document1';
      }
      document.getElementById("documenteditor_title_contentEditor").contentEditable = 'false';
      this.$refs.doceditcontainer.ej2Instances.documentEditor.documentName = document.getElementById("documenteditor_title_name").textContent;
    },
    titleBarClickEvent: function () {
      this.updateDocumentEditorTitle();
    },
    updateDocumentEditorTitle: function () {
      document.getElementById("documenteditor_title_contentEditor").contentEditable = 'true';
      document.getElementById("documenteditor_title_contentEditor").focus();
      window.getSelection().selectAllChildren(document.getElementById("documenteditor_title_contentEditor"));
    },
    documentChangedEvent: function () {
      var obj = this.$refs.doceditcontainer.ej2Instances.documentEditor;
      this.documentTitle = obj.documentName === '' ? 'Untitled Document' : obj.documentName;
      document.getElementById("documenteditor_title_name").textContent = obj.documentName ;
      setTimeout(() => { obj.scrollToPage(1); }, 10);
    },
    updateDocumentEditorSize: function () {
      //Resizes the document editor component to fit full browser window. (Reduced title bar left padding which is 18)
      var windowWidth = window.innerWidth -18;
      //Reducing the size of title bar, to fit Document editor component in remaining height.
      var windowHeight = window.innerHeight - (document.getElementById("documenteditor_titlebar").offsetHeight +26);
      this.$refs.doceditcontainer.ej2Instances.resize(windowWidth, windowHeight);
    },
    onWindowResize: function () {
      //Resizes the document editor component to fit full browser window automatically whenever the browser resized.
      this.updateDocumentEditorSize();
    },
    openTemplate: function () {
      var uploadDocument = new FormData();
      uploadDocument.append('DocumentName', 'Getting Started.docx');
      var loadDocumentUrl =
        this.$refs.doceditcontainer.ej2Instances.serviceUrl + 'LoadDocument';
      var httpRequest = new XMLHttpRequest();
      httpRequest.open('POST', loadDocumentUrl, true);
      var dataContext = this;
      httpRequest.onreadystatechange = function () {
        if (httpRequest.readyState === 4) {
          if (httpRequest.status === 200 || httpRequest.status === 304) {
            //Opens the SFDT for the specified file received from the web API.
            dataContext.$refs.doceditcontainer.ej2Instances.documentEditor.open(
              httpRequest.responseText
            );
          }
        }
      };
      //Sends the request with template file name to web API.
      httpRequest.send(uploadDocument);
    }
  },
  mounted() {
    this.$nextTick(function () {
      this.$refs.doceditcontainer.ej2Instances.locale = 'en-US';
      this.$refs.doceditcontainer.ej2Instances.serviceUrl = this.serviceUrl;
      this.$refs.doceditcontainer.ej2Instances.documentChange = () => {
        this.documentChangedEvent();
      };
      var obj = this;
      setTimeout(() => {
        obj.updateDocumentEditorSize();
      }, 100);
      window.addEventListener('resize', obj.onWindowResize);
      this.openTemplate();
    });
  },
};
</script>
