<script>
  import ComponentsHierarchyChildren from "./ComponentsHierarchyChildren.svelte"

  import { last, sortBy, map, trimCharsStart, trimChars, join } from "lodash/fp"
  import ConfirmDialog from "components/common/ConfirmDialog.svelte"
  import { pipe } from "components/common/core"
  import { store } from "builderStore"
  import { ArrowDownIcon, ShapeIcon } from "components/common/Icons/"

  export let screens = []

  let confirmDeleteDialog
  let componentToDelete = ""

  const joinPath = join("/")

  const normalizedName = name =>
    pipe(
      name,
      [
        trimCharsStart("./"),
        trimCharsStart("~/"),
        trimCharsStart("../"),
        trimChars(" "),
      ]
    )

  const lastPartOfName = c => {
    if (!c) return ""
    const name = c.name ? c.name : c._component ? c._component : c
    return last(name.split("/"))
  }

  const isComponentSelected = (current, comp) => current === comp

  const isFolderSelected = (current, folder) => isInSubfolder(current, folder)

  $: _screens = pipe(
    screens,
    [map(c => ({ component: c, title: lastPartOfName(c) })), sortBy("title")]
  )

  const isScreenSelected = component =>
    component.component &&
    $store.currentPreviewItem &&
    component.component.name === $store.currentPreviewItem.name

  const confirmDeleteComponent = component => {
    componentToDelete = component
    confirmDeleteDialog.show()
  }
</script>

<div class="root">

  {#each _screens as screen}
    <div
      class="budibase__nav-item component"
      class:selected={$store.currentComponentInfo._id === screen.component.props._id}
      on:click|stopPropagation={() => store.setCurrentScreen(screen.title)}>

      <span
        class="icon"
        class:rotate={$store.currentPreviewItem.name !== screen.title}>
        {#if screen.component.props._children.length}
          <ArrowDownIcon />
        {/if}
      </span>

      <span class="icon">
        <ShapeIcon />
      </span>

      <span class="title">{screen.title}</span>
    </div>

    {#if $store.currentPreviewItem.name === screen.title && screen.component.props._children}
      <ComponentsHierarchyChildren
        components={screen.component.props._children}
        currentComponent={$store.currentComponentInfo}
        onSelect={store.selectComponent}
        onDeleteComponent={confirmDeleteComponent}
        onMoveUpComponent={store.moveUpComponent}
        onMoveDownComponent={store.moveDownComponent}
        onCopyComponent={store.copyComponent} />
    {/if}
  {/each}

</div>

<ConfirmDialog
  bind:this={confirmDeleteDialog}
  title="Confirm Delete"
  body={`Are you sure you wish to delete this '${lastPartOfName(componentToDelete)}' component?`}
  okText="Delete Component"
  onOk={() => store.deleteComponent(componentToDelete)} />

<style>
  .root {
    font-weight: 400;
    color: #000333;
  }

  .title {
    margin-left: 10px;
    margin-top: 2px;
    font-size: 13px;
  }

  .icon {
    display: inline-block;
    transition: 0.2s;
    width: 20px;
    margin-top: 2px;
    color: #333;
  }

  .icon:nth-of-type(2) {
    width: 14px;
    margin: 0 0 0 5px;
  }

  :global(svg) {
    transition: 0.2s;
  }

  .rotate :global(svg) {
    transform: rotate(-90deg);
  }
</style>
