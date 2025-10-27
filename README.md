<html data-sly-use.clientLib="/libs/granite/sightly/templates/clientlib.html">
<head>
  <title>Pathfield Example</title>
  <sly data-sly-call="${clientLib.all @ categories='granite.ui.coral.foundation'}"/>
</head>
<body class="coral--light">
  <h2>Pick a path</h2>

  <coral-pathfield
      class="coral-Form-field"
      name="pagePath"
      placeholder="Select a page"
      rootpath="/content">
  </coral-pathfield>

  <script>
    Coral.commons.ready(function() {
      const pathField = document.querySelector('coral-pathfield');
      pathField.on('change', function() {
        console.log('Selected Path:', pathField.value);
      });
    });
  </script>
</body>
</html>
