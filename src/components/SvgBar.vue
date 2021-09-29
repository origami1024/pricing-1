<template>
  <g>
    <rect :x="x" :y="200 - height" width="50" :height="height" :fill="fill" />
    <text :fill="textColor" :x="x + 25" y="192" dominant-baseline="middle" text-anchor="middle">
      {{ value }} rub
    </text>
    <text fill="#fff" :x="x + 25" y="-26" dominant-baseline="middle" text-anchor="middle">
      {{ label }}
    </text>
    <text fill="#fff" :x="x + 25" y="-12" dominant-baseline="middle" text-anchor="middle">
      {{ subLabel }}
    </text>
  </g>
</template>

<script>
  export default {
    props: [
      'x', 'height', 'value', 'label', 'subLabel',
      'fill', 'textColor',
    ],
  }
</script>