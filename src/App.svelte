<script>
  export let name;
  name = "";
  let jsonText = "";
  let jsonData;
  var keyTracking = 0;

  console.log("working changes");

  function download(filename, text) {
    var element = document.createElement("a");
    element.setAttribute(
      "href",
      "data:text/plain;charset=utf-8," + encodeURIComponent(text)
    );
    element.setAttribute("download", filename);
    element.style.display = "none";
    document.body.appendChild(element);
    element.click();
    document.body.removeChild(element);
  }

  function findImageHash(data1, parent) {
    // var newElement;
    // console.log(data1);

    if (data1.type == "GROUP") {
      alert("There is a group element, remove it");
    }

    if (data1.hasOwnProperty("fills")) {
      // console.log(data1.fills);
      if (data1.fills.hasOwnProperty("0")) {
        if (data1.fills[0].hasOwnProperty("imageHash")) {
          let image_id = data1.name;
          //modify gloval jsonData
          data1.fills[0].imageHash =
            "https://images.unsplash.com/" +
            image_id /*+ "&auto=format&fit=crop&w=1080&q=80" */;
        }
      }
    }

    var child = data1;
    if (data1.hasOwnProperty("children")) {
      Object.keys(data1.children).forEach(function (key) {
        var child = findImageHash(data1.children[key], data1);

        // if(!child){
        // 	return false;
        // }else{
        // 	return data1;
        // }
      });
    }

    return child;
  } //eof

  function jsonOnChange(e) {
    jsonText = e.target.value.trim().replace(/^\[([\s\S]*)]$/, "$1");
    return;

    let jsonTextObj = JSON.parse(jsonText);
    jsonData = jsonTextObj;
    var modified_json = findImageHash(jsonTextObj, jsonTextObj);
    console.log(modified_json);

    jsonText = JSON.stringify(modified_json, null, 2);
    // download(name + ".json", jsonText);
    // jsonText = "-------------";
  }

  function jsonTriggerChange() {
    // jsonText = e.target.value.trim().replace(/^\[([\s\S]*)]$/, "$1");

    jsonText = jsonText.trim().replace(/^\[([\s\S]*)]$/, "$1");

    let jsonTextObj = JSON.parse(jsonText);
    jsonData = jsonTextObj;
    var modified_json = findImageHash(jsonTextObj, jsonTextObj);
    console.log(modified_json);

    jsonText = JSON.stringify(modified_json, null, 2);
    // download(name + ".json", jsonText);
    // jsonText = "-------------";
  }

  function downloadJson() {
    download(name + ".json", jsonText);
  }
</script>

<main>
  <h1>Figit Single Component Editor</h1>
  <label for="formID">Component ID: </label><input
    id="formID"
    bind:value={name}
    placeholder="eg: 02_wp_up_30"
  />

  <label for="formID">List JSON: </label>
  <textarea
    value={jsonText}
    rows="20"
    class="jsonTextArea"
    on:change={jsonOnChange}
  />

  <button on:click={jsonTriggerChange}>Add Images</button>

  <button on:click={downloadJson}>Download JSON File</button>
</main>

<style>
  main {
    padding: 1em;
    max-width: 240px;
    margin: 0 auto;
  }

  .jsonTextArea {
    width: 100%;
  }

  h1 {
    color: #ff3e00;
    text-transform: uppercase;
    font-size: 4em;
    font-weight: 100;
  }

  @media (min-width: 640px) {
    main {
      max-width: none;
    }
  }

  label {
    font-weight: 800;
    margin-top: 10px;
  }
</style>
