<sly data-sly-use.clientLib="/libs/granite/sightly/templates/clientlib.html"/>
<!DOCTYPE html>
<html>
<head>
  <title>Method 3: Manual Path Picker - Fixed</title>
  <meta charset="UTF-8">
  
  <!-- Load required libraries -->
  <sly data-sly-call="${clientLib.css @ categories=['coralui3']}"/>
  <sly data-sly-call="${clientLib.js @ categories=['underscore','granite.jquery','coralui3']}"/>
  
  <style>
    .path-input-wrapper {
      display: flex;
      gap: 5px;
      align-items: stretch;
    }
    
    .path-input-wrapper input {
      flex: 1;
    }
    
    .path-input-wrapper button {
      flex-shrink: 0;
    }
    
    .result-box {
      margin-top: 10px;
      padding: 15px;
      border-radius: 4px;
      display: none;
      font-family: monospace;
      word-break: break-all;
    }
    
    .result-page {
      background: #e3f2fd;
      border-left: 4px solid #2196F3;
    }
    
    .result-asset {
      background: #fff3e0;
      border-left: 4px solid #FF9800;
    }
    
    .result-custom {
      background: #e8f5e9;
      border-left: 4px solid #4CAF50;
    }
  </style>
</head>
<body class="coral--light">
  <div style="padding: 20px; max-width: 700px;">
    <h2>Method 3: Manual Path Picker with Custom Button (Fixed)</h2>
    <p><strong>User:</strong> Sureshyarava</p>
    <p><strong>Date:</strong> 2025-10-27 17:41:44 UTC</p>
    
    <hr style="margin: 20px 0;">

    <!-- Page Path Picker -->
    <div style="margin-bottom: 30px;">
      <h3>🔹 Select Page Path</h3>
      <div class="coral-Form-fieldwrapper">
        <label class="coral-Form-fieldlabel" for="pagePathInput">Page Path</label>
        <div class="path-input-wrapper">
          <input 
            is="coral-textfield" 
            id="pagePathInput" 
            class="coral-Form-field coral-Textfield" 
            name="pagePath" 
            placeholder="/content/..."
            value=""
            readonly>
          <button 
            id="browsePageBtn" 
            class="coral-Button coral-Button--secondary"
            title="Browse Pages"
            type="button">
            <coral-icon icon="folderSearch" size="S"></coral-icon>
            <coral-button-label>Browse</coral-button-label>
          </button>
          <button 
            id="clearPageBtn" 
            class="coral-Button coral-Button--quiet"
            title="Clear"
            type="button">
            <coral-icon icon="close" size="S"></coral-icon>
          </button>
        </div>
      </div>
      <button 
        id="getPagePath" 
        class="coral-Button coral-Button--primary" 
        style="margin-top: 10px;"
        type="button">
        Get Selected Path
      </button>
      <div id="pageResult" class="result-box result-page"></div>
    </div>

    <hr style="margin: 40px 0;">

    <!-- Asset Path Picker -->
    <div style="margin-bottom: 30px;">
      <h3>🔹 Select Asset Path (DAM)</h3>
      <div class="coral-Form-fieldwrapper">
        <label class="coral-Form-fieldlabel" for="assetPathInput">Asset Path</label>
        <div class="path-input-wrapper">
          <input 
            is="coral-textfield" 
            id="assetPathInput" 
            class="coral-Form-field coral-Textfield" 
            name="assetPath" 
            placeholder="/content/dam/..."
            value=""
            readonly>
          <button 
            id="browseAssetBtn" 
            class="coral-Button coral-Button--secondary"
            title="Browse Assets"
            type="button">
            <coral-icon icon="image" size="S"></coral-icon>
            <coral-button-label>Browse</coral-button-label>
          </button>
          <button 
            id="clearAssetBtn" 
            class="coral-Button coral-Button--quiet"
            title="Clear"
            type="button">
            <coral-icon icon="close" size="S"></coral-icon>
          </button>
        </div>
      </div>
      <button 
        id="getAssetPath" 
        class="coral-Button coral-Button--primary" 
        style="margin-top: 10px;"
        type="button">
        Get Selected Asset
      </button>
      <div id="assetResult" class="result-box result-asset"></div>
    </div>

    <hr style="margin: 40px 0;">

    <!-- Custom Path Picker (Editable) -->
    <div style="margin-bottom: 30px;">
      <h3>🔹 Custom Path (Editable)</h3>
      <div class="coral-Form-fieldwrapper">
        <label class="coral-Form-fieldlabel" for="customPathInput">Custom Path</label>
        <div class="path-input-wrapper">
          <input 
            is="coral-textfield" 
            id="customPathInput" 
            class="coral-Form-field coral-Textfield" 
            name="customPath" 
            placeholder="Type or browse..."
            value="">
          <button 
            id="browseCustomBtn" 
            class="coral-Button coral-Button--secondary"
            title="Browse"
            type="button">
            <coral-icon icon="folderSearch" size="S"></coral-icon>
            <coral-button-label>Browse</coral-button-label>
          </button>
          <button 
            id="clearCustomBtn" 
            class="coral-Button coral-Button--quiet"
            title="Clear"
            type="button">
            <coral-icon icon="close" size="S"></coral-icon>
          </button>
        </div>
        <div style="margin-top: 10px;">
          <label class="coral-Checkbox">
            <input class="coral-Checkbox-input" type="checkbox" id="customRootPath" value="/content/dam">
            <span class="coral-Checkbox-checkmark"></span>
            <span class="coral-Checkbox-description">Use DAM path (/content/dam) instead of /content</span>
          </label>
        </div>
      </div>
      <button 
        id="getCustomPath" 
        class="coral-Button coral-Button--primary" 
        style="margin-top: 10px;"
        type="button">
        Get Custom Path
      </button>
      <div id="customResult" class="result-box result-custom"></div>
    </div>

    <!-- Debug Info -->
    <div style="margin-top: 40px; padding: 15px; background: #f5f5f5; border-radius: 4px; font-family: monospace; font-size: 12px;">
      <strong>Debug Info:</strong>
      <div id="debugInfo"></div>
    </div>
  </div>

  <script>
    (function(document, $) {
      'use strict';

      $(document).ready(function() {
        console.log('✓ Document ready');
        
        // Debug: Check libraries
        var debugInfo = document.getElementById('debugInfo');
        var status = [];
        status.push('Underscore: ' + (typeof _ !== 'undefined' ? '✓ Loaded' : '✗ Missing'));
        status.push('jQuery: ' + (typeof $ !== 'undefined' ? '✓ Loaded' : '✗ Missing'));
        status.push('Coral: ' + (typeof Coral !== 'undefined' ? '✓ Loaded' : '✗ Missing'));
        status.push('Granite: ' + (typeof Granite !== 'undefined' ? '✓ Loaded' : '✗ Missing'));
        debugInfo.innerHTML = status.join('<br>');
        
        // Wait for Coral UI
        Coral.commons.ready(function() {
          console.log('✓ Coral UI ready');
          
          // Function to open path picker in dialog
          function openPathPicker(inputElement, options) {
            var defaults = {
              rootPath: '/content',
              filter: 'hierarchyNotFile',
              title: 'Select Path'
            };
            
            var config = $.extend({}, defaults, options);
            var currentPath = inputElement.value || config.rootPath;
            
            // Build picker URL
            var pickerUrl = '/libs/granite/ui/content/coral/foundation/form/pathfield/picker.html' +
              '?root=' + encodeURIComponent(config.rootPath) +
              '&path=' + encodeURIComponent(currentPath) +
              '&filter=' + config.filter +
              '&selectionCount=single' +
              '&_charset_=utf-8';
            
            console.log('Opening picker:', pickerUrl);
            
            // Create Coral Dialog
            var dialog = new Coral.Dialog().set({
              id: 'pathPickerDialog_' + Date.now(), // Unique ID
              variant: 'default',
              closable: true,
              modal: true,
              backdrop: Coral.Dialog.backdrop.STATIC,
              header: {
                innerHTML: config.title
              },
              content: {
                innerHTML: '<iframe id="pickerFrame" src="' + pickerUrl + '" style="width:100%; height:500px; border:none; display:block;"></iframe>'
              },
              footer: {
                innerHTML: '<button class="coral-Button coral-Button--default" coral-close>Cancel</button>'
              }
            });
            
            // Add to DOM
            document.body.appendChild(dialog);
            
            // Show dialog after a brief delay to ensure DOM is ready
            setTimeout(function() {
              dialog.show();
            }, 100);
            
            // Listen for selection via postMessage
            var messageHandler = function(event) {
              console.log('Received message:', event.data);
              
              // Handle path selection
              if (event.data && event.data.type === 'foundation-picker-item-selected') {
                if (event.data.item && event.data.item.value) {
                  inputElement.value = event.data.item.value;
                  console.log('✓ Selected path:', event.data.item.value);
                  
                  // Trigger change event
                  $(inputElement).trigger('change');
                  
                  // Close dialog
                  dialog.hide();
                  
                  // Cleanup
                  setTimeout(function() {
                    window.removeEventListener('message', messageHandler);
                    if (dialog.parentNode) {
                      dialog.parentNode.removeChild(dialog);
                    }
                  }, 300);
                }
              }
            };
            
            window.addEventListener('message', messageHandler);
            
            // Cleanup on dialog close
            dialog.on('coral-overlay:close', function() {
              console.log('Dialog closed');
              window.removeEventListener('message', messageHandler);
              setTimeout(function() {
                if (dialog.parentNode) {
                  dialog.parentNode.removeChild(dialog);
                }
              }, 300);
            });
          }

          // Get all elements AFTER Coral is ready
          var pagePathInput = document.getElementById('pagePathInput');
          var browsePageBtn = document.getElementById('browsePageBtn');
          var clearPageBtn = document.getElementById('clearPageBtn');
          var getPagePathBtn = document.getElementById('getPagePath');
          var pageResult = document.getElementById('pageResult');

          // Page Path Picker - Use event delegation
          if (browsePageBtn) {
            // Remove any existing listeners
            var newBrowsePageBtn = browsePageBtn.cloneNode(true);
            browsePageBtn.parentNode.replaceChild(newBrowsePageBtn, browsePageBtn);
            browsePageBtn = newBrowsePageBtn;
            
            browsePageBtn.addEventListener('click', function(e) {
              e.preventDefault();
              e.stopPropagation();
              console.log('Browse page button clicked');
              openPathPicker(pagePathInput, {
                rootPath: '/content',
                filter: 'hierarchyNotFile',
                title: 'Select Page'
              });
              return false;
            });
          }

          if (clearPageBtn) {
            clearPageBtn.addEventListener('click', function(e) {
              e.preventDefault();
              pagePathInput.value = '';
              pageResult.style.display = 'none';
            });
          }

          if (getPagePathBtn) {
            getPagePathBtn.addEventListener('click', function() {
              var path = pagePathInput.value;
              pageResult.style.display = 'block';
              pageResult.innerHTML = '<strong>Selected Page Path:</strong><br>' + (path || '<em>(no path selected)</em>');
              console.log('Page path:', path);
            });
          }

          // Asset Path Picker
          var assetPathInput = document.getElementById('assetPathInput');
          var browseAssetBtn = document.getElementById('browseAssetBtn');
          var clearAssetBtn = document.getElementById('clearAssetBtn');
          var getAssetPathBtn = document.getElementById('getAssetPath');
          var assetResult = document.getElementById('assetResult');

          if (browseAssetBtn) {
            var newBrowseAssetBtn = browseAssetBtn.cloneNode(true);
            browseAssetBtn.parentNode.replaceChild(newBrowseAssetBtn, browseAssetBtn);
            browseAssetBtn = newBrowseAssetBtn;
            
            browseAssetBtn.addEventListener('click', function(e) {
              e.preventDefault();
              e.stopPropagation();
              console.log('Browse asset button clicked');
              openPathPicker(assetPathInput, {
                rootPath: '/content/dam',
                filter: 'nosystem',
                title: 'Select Asset'
              });
              return false;
            });
          }

          if (clearAssetBtn) {
            clearAssetBtn.addEventListener('click', function(e) {
              e.preventDefault();
              assetPathInput.value = '';
              assetResult.style.display = 'none';
            });
          }

          if (getAssetPathBtn) {
            getAssetPathBtn.addEventListener('click', function() {
              var path = assetPathInput.value;
              assetResult.style.display = 'block';
              assetResult.innerHTML = '<strong>Selected Asset Path:</strong><br>' + (path || '<em>(no asset selected)</em>');
              console.log('Asset path:', path);
            });
          }

          // Custom Path Picker
          var customPathInput = document.getElementById('customPathInput');
          var browseCustomBtn = document.getElementById('browseCustomBtn');
          var clearCustomBtn = document.getElementById('clearCustomBtn');
          var getCustomPathBtn = document.getElementById('getCustomPath');
          var customResult = document.getElementById('customResult');
          var customRootPathCheckbox = document.getElementById('customRootPath');

          if (browseCustomBtn) {
            var newBrowseCustomBtn = browseCustomBtn.cloneNode(true);
            browseCustomBtn.parentNode.replaceChild(newBrowseCustomBtn, browseCustomBtn);
            browseCustomBtn = newBrowseCustomBtn;
            
            browseCustomBtn.addEventListener('click', function(e) {
              e.preventDefault();
              e.stopPropagation();
              console.log('Browse custom button clicked');
              var useDAM = customRootPathCheckbox && customRootPathCheckbox.checked;
              openPathPicker(customPathInput, {
                rootPath: useDAM ? '/content/dam' : '/content',
                filter: useDAM ? 'nosystem' : 'hierarchyNotFile',
                title: 'Select Custom Path'
              });
              return false;
            });
          }

          if (clearCustomBtn) {
            clearCustomBtn.addEventListener('click', function(e) {
              e.preventDefault();
              customPathInput.value = '';
              customResult.style.display = 'none';
            });
          }

          if (getCustomPathBtn) {
            getCustomPathBtn.addEventListener('click', function() {
              var path = customPathInput.value;
              customResult.style.display = 'block';
              customResult.innerHTML = '<strong>Custom Path:</strong><br>' + (path || '<em>(no path entered)</em>');
              console.log('Custom path:', path);
            });
          }

          console.log('✓ Manual path picker initialized successfully');
        });
      });

    })(document, Granite.$);
  </script>
</body>
</html>
