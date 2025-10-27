<html data-sly-use.clientLib="/libs/granite/sightly/templates/clientlib.html">
<head>
  <title>Pathfield Example</title>
  <sly data-sly-call="${clientLib.all @ categories='granite.ui.coral.foundation'}"/>
</head>
<body class="coral--light coral-Form coral-Form--vertical">
  <h2>Select a Path</h2>

  <!-- ✅ This is the correct way to render a Pathfield in AEM 6.5 -->
  <sly
    data-sly-resource="${'pathfield' @
      resourceType='granite/ui/components/coral/foundation/form/pathfield',
      name='./pagePath',
      fieldLabel='Select a Page',
      rootPath='/content',
      required=true
    }"
  ></sly>

  <button id="getValue" class="coral-Button coral-Button--primary">Get Value</button>

  <script>
    Coral.commons.ready(function() {
      const field = document.querySelector('[name="./pagePath"]');
      document.querySelector('#getValue').addEventListener('click', () => {
        console.log('Selected Path:', field.value);
        alert('Selected Path: ' + field.value);
      });
    });
  </script>
</body>
</html>
