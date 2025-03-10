<script>
  import { getContext, onMount } from "svelte";
  import { Icon } from "svelte-chota";
  import { _ } from "svelte-i18n";

  /* Icons https://materialdesignicons.com/ */
  import {
    mdiAccount,
    mdiPlus,
    mdiCardBulleted,
    mdiCog,
    mdiArchiveArrowDown,
    mdiArchiveArrowUp,
    mdiLock,
  } from "@mdi/js";

  import Dropdown from "../../../components/Elements/Dropdown.svelte";

  //Stores
  import {
    currentAccount,
    currentNetwork,
    accountStore,
  } from "../../../common/stores.js";
  import { Blockies } from "../../../common/blockies.js";
  import {
    openPageWithPath,
    fromNano,
    shortAddress,
  } from "../../../common/utils.js";

  let allAccounts = [];

  const getAllAccounts = () => {
    browser.runtime
      .sendMessage({
        type: "getAllAccounts",
        data: ["nickname", "address", "balance", "deployed"],
      })
      .then((result) => {
        allAccounts = result;
      });
  };

  const changeAccount = (target) => {
    let accountAddress;
    if (target.dataset) {
      accountAddress = target.dataset.value;
    }
    const deep = 5; // search optimization and lock protection
    let counter = 0;
    let parentElement;
    while ((parentElement = target.parentElement)) {
      if (counter > deep) {
        break;
      }
      if (parentElement.dataset) {
        accountAddress = parentElement.dataset.value;
        break;
      } else {
        target = parentElement;
      }
      counter++;
    }
    const newCurrentAccount = allAccounts.filter(
      (item) => item.address == accountAddress
    );
    if (newCurrentAccount.length) {
      accountStore.changeAccount(newCurrentAccount[0]);
    }
  };

  const blockies = new Blockies();
  $: currentIdenticon = blockies.createIcon({
    seed: $currentAccount.address,
    color: "#2479b9",
    bgcolor: "#151515",
    spotcolor: "#33ff00",
  });

  const indenticon = (address) => {
    return blockies.createIcon({
      seed: address,
      color: "#2479b9",
      bgcolor: "#151515",
      spotcolor: "#33ff00",
    });
  };

  const lock = () => {
    browser.runtime.sendMessage({ type: "lockWallet" });
    return;
  };

  onMount(() => {
    getAllAccounts();
  });

  currentAccount.subscribe((value) => {
    getAllAccounts();
  });

  const addNewAccount = () => {
    openPageWithPath("AddNewAccount");
  };
  const restoreWallet = () => {
    openPageWithPath("RestoreWallet");
  };
  const backupWallet = () => {
    openPageWithPath("Backup");
  };
  const settings = () => {
    openPageWithPath("Settings");
  };
  const information = () => {
    openPageWithPath("About");
  };
</script>

<style lang="scss">
  :global(.account-dropdown summary) {
    font-size: 0rem;
    width: 3.25rem;
    padding: 0.2rem;
    margin-right: 1rem;
    margin-top: 0.5rem;
    color: var(--color-black);
    display: initial;
    border-radius: 50%;
    font-size: 0rem;
  }
  :global(.account-dropdown summary:focus, .account-dropdown
      .button.outline.icon
      .lefticon:focus) {
    outline: none;
  }
  :global(.account-dropdown .card) {
    left: -16.5rem !important;
    opacity: 0.9;
    font-size: 1.25rem;
    padding: 0 !important;
    top: 4.5rem;
    width: 20rem;
  }
  :global(.account-dropdown .card .selected) {
    background-color: var(--color-primary) !important;
  }
  :global(.account-dropdown .card div) {
    padding: 0.5rem 2rem;
  }
  :global(.account-dropdown .card .account-item) {
    display: flex;
    flex-direction: row;
    cursor: pointer;
  }
  :global(.account-dropdown .card .menu-item) {
    cursor: pointer;
  }
  :global(.account-dropdown .button.outline) {
    border: var(--color-black) 1px solid;
  }
  :global(.account-dropdown .card .account-item .identicon) {
    border: var(--color-black) 1px solid;
    display: inline-grid;
    width: fit-content;
    height: fit-content;
    border-radius: 50%;
    padding: 0.2rem;
    margin-right: 1rem;
  }
  :global(.account-dropdown .card .account-item .nickname) {
    max-width: 85px;
    overflow: hidden;
    text-overflow: ellipsis;
    width: 85px;
  }
  :global(.account-dropdown .card .account-item .balance) {
    font-weight: bold;
  }
  .lock-wallet {
    .flex-row {
      justify-content: space-between;
    }
    div {
      padding: 0.5rem 0;
    }
    .lock-wallet-button {
      cursor: pointer;
    }
  }
  .account-list-wrapper {
    max-height: 20rem;
    overflow: hidden;
    padding: 0px;
    width: 100%;
    .account-list-wrapper-scroll {
      max-height: 20rem;
      overflow-y: auto;
      padding: 0px;
      width: calc(100% + 20px);
    }
  }
  :global(.account-dropdown .spacer) {
    border-top: dashed 1px var(--color-black);
    margin-top: 0.5rem;
  }
</style>

<div class="flex-row account-dropdown">
  <Dropdown dropdown="" svg={currentIdenticon} autoclose outline>
    <div class="lock-wallet">
      <div class="flex-row">
        <div>{$_('Accounts')}</div>
        <div class="lock-wallet-button" on:click={() => lock()}>
          <Icon src={mdiLock} size="1" color="var(--color-black)" />
          {$_('Sign out')}
        </div>
      </div>
    </div>
    <div class="spacer" />
    <div class="account-list-wrapper">
      <div class="account-list-wrapper-scroll">
        {#each allAccounts as account}
          <div
            on:click={(event) => changeAccount(event.target)}
            class:selected={account.address == $currentAccount.address}
            data-value={account.address}
            class="account-item">
            <span class="identicon">{@html indenticon(account.address)}</span>
            <span
              class="nickname is-vertical-align"
              title={shortAddress(account.address)}>{account.nickname}</span>
            <span class="balance is-center">
              {account.balance[$currentNetwork.server] ? fromNano(account.balance[$currentNetwork.server], 2) : 0}
            </span>
          </div>
        {/each}
      </div>
    </div>
    <div class="spacer" />
    <div class="menu-item" on:click={() => addNewAccount()}>
      <Icon src={mdiPlus} size="1.5" color="var(--color-black)" />
      {$_('Add')}
    </div>
    <div class="spacer" />
    <div class="menu-item" on:click={() => restoreWallet()}>
      <Icon src={mdiArchiveArrowUp} size="1.5" color="var(--color-black)" />
      {$_('Restore wallet')}
    </div>
    <div class="menu-item" on:click={() => backupWallet()}>
      <Icon src={mdiArchiveArrowDown} size="1.5" color="var(--color-black)" />
      {$_('Backup wallet')}
    </div>
    <div class="spacer" />
    <div class="menu-item" on:click={() => settings()}>
      <Icon src={mdiCog} size="1.5" color="var(--color-black)" />
      {$_('Settings')}
    </div>
    <div class="menu-item" on:click={() => information()}>
      <Icon src={mdiCardBulleted} size="1.5" color="var(--color-black)" />
      {$_('Information')}
    </div>
  </Dropdown>
</div>
