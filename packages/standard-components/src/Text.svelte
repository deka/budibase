<script>
  import { buildStyle } from "./buildStyle"

  export let text = ""
  export let className = ""

  export let formattingTag = ""

  export let fontFamily = ""
  export let fontSize = "1em"
  export let textAlign = ""
  export let verticalAlign = ""
  export let color = ""

  export let _bb

  const isTag = tag => (formattingTag || "").indexOf(tag) > -1

  $: style = buildStyle({
    "font-size": fontSize,
    "font-family": fontFamily,
    color,
  })
</script>

{#if isTag('none')}
  <span {style}>{text}</span>
{:else if isTag('<b>')}
  <b class={className} {style}>{text}</b>
{:else if isTag('<strong>')}
  <strong class={className} {style}>{text}</strong>
{:else if isTag('<i>')}
  <i class={className} {style}>{text}</i>
{:else if isTag('<em>')}
  <em class={className} {style}>{text}</em>
{:else if isTag('<mark>')}
  <mark class={className} {style}>{text}</mark>
{:else if isTag('<small>')}
  <small class={className} {style}>{text}</small>
{:else if isTag('<del>')}
  <del class={className} {style}>{text}</del>
{:else if isTag('<ins>')}
  <ins class={className} {style}>{text}</ins>
{:else if isTag('<sub>')}
  <sub class={className} {style}>{text}</sub>
{:else if isTag('<sup>')}
  <sup class={className} {style}>{text}</sup>
{:else}{text}{/if}
