<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <title>Pathfield Example</title>
  <link rel="stylesheet" href="/etc/clientlibs/granite/coralui3.css" type="text/css">
  <script src="/etc/clientlibs/granite/jquery.js"></script>
  <script src="/etc/clientlibs/granite/utils.js"></script>
  <script src="/etc/clientlibs/granite/coralui3.js"></script>
</head>
<body>
  <coral-pathfield
      id="myPathfield"
      placeholder="Select a path"
      name="pagePath"
      rootpath="/content"
      value="">
  </coral-pathfield>

  <script>
    Coral.commons.ready(function() {
      const pathfield = document.querySelector('#myPathfield');
      console.log('Selected Path:', pathfield.value);
    });
  </script>
</body>
</html>
