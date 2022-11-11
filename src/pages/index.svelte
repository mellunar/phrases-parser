<script>
  let finalObject = {};
  let parsed, unparsed, finalJson;

  let copied = false;
  let failed = false;
  let invalid = false;

  function parseJson(input) {
    const { value } = input.target;
    unparsed = value;

    if (input?.key === "Enter" || input?.keyCode === 13) {
      submit();
    }
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

    try {
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
    } catch {
      invalid = true;

      setTimeout(() => {
        invalid = false;
      }, 5000);
    }
  }

  async function copyData() {
    await navigator.clipboard
      .writeText(finalJson)
      .then(() => {
        copied = true;

        setTimeout(() => {
          copied = false;
        }, 2000);
      })
      .catch(() => {
        failed = true;

        setTimeout(() => {
          failed = false;
        }, 2000);
      });
  }
</script>

<main class="main">
  <div class="content">
    <textarea class="textarea" on:keyup={parseJson} />
    <button
      class="button"
      disabled={!unparsed || unparsed === ""}
      on:click={submit}>Parse to object</button
    >

    {#if finalJson}
      <div class="parsed" role="button" on:click={copyData}>
        <div class="final-box">{finalJson}</div>

        <div class="message" class:green={copied} class:red={failed}>
          {#if !copied && !failed}
            Click to copy
          {/if}

          {#if copied}
            Copied!
          {/if}

          {#if failed}
            Failed to copy
          {/if}
        </div>
      </div>
    {/if}

    {#if invalid}
      <p class="invalid red">Invalid JSON format</p>
    {/if}
  </div>
</main>

<style src="./index.scss"></style>
