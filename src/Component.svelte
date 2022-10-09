<script>
  import { getContext } from "svelte"
  const { styleable } = getContext("sdk")
  const component = getContext("component")
  export let width = 640;
  export let height = 360;
  export let urlString = '';

  const getIdFromUrl = (url) => {
    try {
      const searchParams = new URL(url)?.searchParams;

      if (searchParams.get('list')) {
        return searchParams.get('list');
      } else {
        return searchParams.get('v');
      }
    } catch (e) {
      console.error('failed to parse searchParams');
      return e;
    }
  };

  const embedUrl = (id, type) => {
    const embedVideo = (id) => `https://www.youtube.com/embed/${id}`;
    const embedList = (id) => `https://www.youtube.com/embed?listType=playlist&list=${id}`;
    if (type === 'list') {
      return embedList(id);
    }
    return embedVideo(id);
  }

  function checkUrlValid(url) {
    return (url.includes("https://www.youtube.com/watch?v"));
  }

  $: isValid = checkUrlValid(urlString);
  $: fileType = urlString.includes('list=') ? 'list' : 'video';
  $: urlAsEmbedLink = embedUrl(getIdFromUrl(urlString), fileType);
</script>

<div use:styleable={$component.styles}>
  {#if !urlString}
    <p>Please enter a Youtube url</p>
  {:else if isValid}
    <iframe id="player" src={urlAsEmbedLink} width={width} height={height}></iframe>
  {:else}
    <p>Error - Please enter a valid Youtube url</p>
  {/if}
</div>