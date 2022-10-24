<script>
  let finalObject = {};
  let parsed, unparsed, finalJson;

  function parseJson(input) {
    const { value } = input.target;
    unparsed = value;
  }

  function stringToObj(str) {
    const obj = {};
    let container = obj;

    const regex = str.match(/[^.]+/g);
    regex.forEach((e, i) => {
      container = container[e] = i == regex.length - 1 ? parsed[str] : {};
    });

    return obj;
  }

  function submit() {
    if (!unparsed || unparsed === "") {
      return;
    }

    parsed = JSON.parse(unparsed);

    const keys = Object.keys(parsed);

    keys.forEach((key) => {
      const newObject = stringToObj(key);

      const newKeys = Object.keys(newObject);

      if (!finalObject[newKeys[0]]) {
        finalObject = { ...finalObject, ...newObject };
      } else {
        finalObject[newKeys[0]] = {
          ...finalObject[newKeys[0]],
          ...newObject[newKeys[0]],
        };
      }
    });

    finalJson = JSON.stringify(finalObject);
  }
</script>

<main>
  <textarea on:change={parseJson} />
  <button on:click={submit}>Parse to object</button>

  {#if finalJson}
    {finalJson}
  {/if}
</main>
