<script>
  import logo from '$lib/assets/logo.svg'
  import { browser } from '$app/environment'
  import { page } from '$app/stores'

  const { supabase } = $page.data

  let image_url = logo
  if (browser && supabase) {
    supabase.storage
      .from('images')
      .download(`logo.svg`)
      .then(({ data, error }) => {
        if (data) {
          var reader = new FileReader()
          reader.onload = function () {
            image_url = reader.result
          }
          reader.readAsDataURL(data)
        }
      })
  }
</script>

<div class="logo">
  <!-- svelte-ignore a11y-missing-attribute -->
  <img src={image_url} />
</div>

<style>
  .logo {
    width: 100%;
  }
  img {
    width: 100%;
  }
</style>
