<script>
  import { getContext, onDestroy } from "svelte";
  import {
    CellAttachment,
    CellAttachmentExpanded,
    SuperField,
  } from "@poirazis/supercomponents-shared";

  const { styleable, builderStore, Provider, API } = getContext("sdk");
  const component = getContext("component");

  const formContext = getContext("form");
  const formStepContext = getContext("form-step");
  const groupLabelPosition = getContext("field-group");
  const labelWidth = getContext("field-group-label-width");
  const groupColumns = getContext("field-group-columns");
  const groupDisabled = getContext("field-group-disabled");
  const formApi = formContext?.formApi;

  export let field;
  export let controlType = "select";
  export let imageRatio = "landscape";
  export let gridColumns = 4;

  export let label;
  export let span = 6;
  export let placeholder;
  export let disabled;
  export let readonly;
  export let validation;
  export let defaultValue;

  export let onChange;
  export let autofocus;

  export let icon;

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
    disabled: disabled || groupDisabled || fieldState?.disabled,
    padding: "0.5rem",
    readonly: readonly || fieldState?.readonly,
    icon,
    controlType,
    imageRatio,
    gridColumns,
    error: fieldState?.error,
    role,
  };

  $: $component.styles = {
    ...$component.styles,
    normal: {
      ...$component.styles.normal,
      "max-height": $component.styles.normal.height
        ? $component.styles.normal.height
        : "15rem",
      overflow: "hidden",
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
  <Provider data={{ value }} />
  <SuperField
    multirow={controlType != "select"}
    {labelPos}
    {labelWidth}
    {field}
    {label}
    {error}
    {helpText}
  >
    {#if controlType == "select"}
      <CellAttachment
        {cellOptions}
        {value}
        {fieldSchema}
        {autofocus}
        {API}
        tableid={formContext?.dataSource?.tableId}
        on:change={(e) => handleChange(e.detail)}
      />
    {:else}
      <CellAttachmentExpanded
        {cellOptions}
        {value}
        {fieldSchema}
        {autofocus}
        {API}
        tableid={formContext?.dataSource?.tableId}
        on:change={(e) => handleChange(e.detail)}
      />
    {/if}
  </SuperField>
</div>
