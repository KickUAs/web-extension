<script>
  import { _ } from "svelte-i18n";
  import { Icon, Card } from "svelte-chota";
  let version = browser.runtime.getManifest().version;

  import {
    mdiFacebook,
    mdiTwitter,
    mdiLinkedin,
    mdiXing,
    mdiVk,
    mdiReddit,
    mdiWhatsapp,
    mdiSkype,
    mdiTelegram,
    mdiFacebookMessenger
  } from "@mdi/js";

  const icons = {
    "facebook": mdiFacebook,
    "twitter": mdiTwitter,
    "linkedin": mdiLinkedin,
    "xing": mdiXing,
    "vk": mdiVk,
    "reddit": mdiReddit,
    "whatsapp": mdiWhatsapp,
    "skype": mdiSkype,
    "telegram": mdiTelegram,
    "facebookMessenger": mdiFacebookMessenger
  };

  const openLink = (url) => {
    url = url.replace('{{TEMPLATE_TEXT}}', $_("Try Free TON blockchain web-extension"));
    url = url.replace('{{TEMPLATE_URL}}', "https://mytonwallet.com/installation");
    url = url.replace('{{TEMPLATE_IMAGE}}', "https://mytonwallet.com/images/big_logo.png");
    window.open(url, "_blank");
  }

  const shareList = [
      {name: "facebook", url: 'http://www.facebook.com/sharer.php?s=100&&p[summary]={{TEMPLATE_TEXT}}&p[url]={{TEMPLATE_URL}}&p[images][0]={{TEMPLATE_IMAGE}}'},
      {name: "twitter", url: 'https://twitter.com/share?url={{TEMPLATE_URL}}&text={{TEMPLATE_TEXT}}'},
      {name: "linkedin", url: 'https://www.linkedin.com/cws/share?url={{TEMPLATE_URL}}'},
      {name: "xing", url: 'https://www.xing.com/social_plugins/share?url={{TEMPLATE_URL}}'},
      {name: "vk", url: 'http://vk.com/share.php?url={{TEMPLATE_URL}}&description={{TEMPLATE_TEXT}}&image={{TEMPLATE_IMAGE}}'},
      {name: "reddit", url: 'http://www.reddit.com/submit?url={{TEMPLATE_URL}}&title={{TEMPLATE_TEXT}}'},
      {name: "whatsapp", url: 'whatsapp://send?text={{TEMPLATE_URL}} {{TEMPLATE_TEXT}}'},
      {name: "skype", url: 'skype:?chat&topic={{TEMPLATE_URL}} {{TEMPLATE_TEXT}}'},
      {name: "telegram", url: 'tg://msg?text={{TEMPLATE_URL}} {{TEMPLATE_TEXT}}'},
      {name: "facebookMessenger", url: 'fb-messenger://share?link={{TEMPLATE_URL}}'}
  ];

</script>

<style lang="scss">
  .box {
    display: flex;
    flex-direction: column;
  }

  .about-box {
    margin-top: 1rem;
  }

  .social-box {
    display: flex;
    justify-content: space-evenly;
    width: 534px;
    margin: 70px auto;
    align-items: center;
    div {
      cursor: pointer;
    }
  }
</style>

<div class="box">
  <p class="text-body3"><strong>{$_("Version")}</strong> {version}</p>
  <div class="about-box">
    <Card>
      {$_("About text")}
      <div slot="footer" class="is-right">
        <a href="https://mytonwallet.com" target="_blank" title="{$_("Visit website")}">{$_("Visit website")}</a>
      </div>
    </Card>
  </div>
  <div class="social-box">
    {#each shareList as item}
      <div on:click={() => openLink(item.url)}>
        <Icon src="{icons[item.name]}" size="2"/>
      </div>
    {/each}
  </div>
</div>
