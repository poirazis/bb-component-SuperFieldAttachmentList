<script>
  import { getContext, onDestroy } from "svelte";
  import {
    CellAttachment,
    SuperButton,
    SuperField,
  } from "@poirazis/supercomponents-shared";

  const { styleable, enrichButtonActions, builderStore } = getContext("sdk");
  const component = getContext("component");
  const allContext = getContext("context");

  const formContext = getContext("form");
  const formStepContext = getContext("form-step");
  const groupLabelPosition = getContext("field-group");
  const labelWidth = getContext("field-group-label-width");
  const groupColumns = getContext("field-group-columns");
  const groupDisabled = getContext("field-group-disabled");
  const formApi = formContext?.formApi;

  export let field;
  export let buttons = [];

  export let label;
  export let span = 6;
  export let placeholder;
  export let defaultValue;
  export let template;
  export let disabled;
  export let readonly;
  export let validation;
  export let invisible = false;

  export let onChange;
  export let debounced;
  export let debounceDelay;
  export let autofocus;

  export let icon;
  export let suggestions;
  export let clearValueIcon;

  export let role;
  export let labelPosition = "fieldGroup";
  export let helpText;

  let formField;
  let formStep;
  let fieldState;
  let fieldApi;
  let fieldSchema;
  let value;

  $: formStep = formStepContext ? $formStepContext || 1 : 1;
  $: labelPos =
    groupLabelPosition && labelPosition == "fieldGroup"
      ? groupLabelPosition
      : labelPosition;

  $: formField = formApi?.registerField(
    field,
    "attachment",
    defaultValue,
    disabled,
    readonly,
    validation,
    formStep
  );

  $: unsubscribe = formField?.subscribe((value) => {
    fieldState = value?.fieldState;
    fieldApi = value?.fieldApi;
    fieldSchema = value?.fieldSchema;
  });

  $: value = fieldState?.value ? fieldState.value : defaultValue;
  $: error = fieldState?.error;
  $: cellOptions = {
    placeholder,
    defaultValue,
    disabled: disabled || groupDisabled || fieldState?.disabled,
    template,
    suggestions,
    padding: "0.5rem",
    readonly: readonly || fieldState?.readonly,
    icon,
    debounce: debounced ? debounceDelay : false,
    clearValueIcon,
    error: fieldState?.error,
    role,
  };

  $: $component.styles = {
    ...$component.styles,
    normal: {
      ...$component.styles.normal,
      display:
        invisible && !$builderStore.inBuilder
          ? "none"
          : $component.styles.normal.display,
      opacity: invisible && $builderStore.inBuilder ? 0.6 : 1,
      "grid-column": groupColumns ? `span ${span}` : "span 1",
    },
  };

  const handleChange = (newValue) => {
    onChange?.({ value: newValue });
    fieldApi?.setValue(newValue);
  };

  onDestroy(() => {
    fieldApi?.deregister();
    unsubscribe?.();
  });
</script>

<!-- svelte-ignore a11y-click-events-have-key-events -->
<!-- svelte-ignore a11y-no-noninteractive-tabindex -->
<!-- svelte-ignore a11y-no-noninteractive-element-interactions -->
<div use:styleable={$component.styles}>
  <SuperField {labelPos} {labelWidth} {field} {label} {error} {helpText}>
    <CellAttachment
      {cellOptions}
      {value}
      {fieldSchema}
      {autofocus}
      on:change={(e) => handleChange(e.detail)}
    />
    {#if buttons?.length}
      <div class="inline-buttons">
        {#each buttons as { text, onClick, icon, size, quiet, type }}
          <SuperButton
            {icon}
            {size}
            {disabled}
            {text}
            {quiet}
            {type}
            onClick={enrichButtonActions(onClick, $allContext)}
          />
        {/each}
      </div>
    {/if}
  </SuperField>
</div>
