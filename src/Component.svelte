<script>
  import { getContext, onDestroy } from "svelte";
  import {
    CellAttachment,
    CellAttachmentExpanded,
    CellAttachmentSlider,
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

  export let onClickAction = "view"; // view | download | custom
  export let onChange;
  export let autofocus;

  export let icon;

  export let role;
  export let labelPosition = "fieldGroup";
  export let helpText;

  // Slider Speccific Settings
  export let carouselDots = false;
  export let infinite = false;
  export let speed = 500;
  export let carouselItemsToShow = 1;
  export let carouselItemsToScroll = 1;
  export let carouselArrows = true;

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
    onClickAction,
    error: fieldState?.error,
    role,
    ...(controlType === "slider" && {
      carouselArrows,
      carouselDots,
      carouselItemsToShow,
      carouselItemsToScroll,
    }),
  };

  $: inBuilder = $builderStore?.inBuilder;

  $: $component.styles = {
    ...$component.styles,
    normal: {
      height: controlType != "select" ? "15rem" : "auto",
      ...$component.styles.normal,
      overflow: "hidden",
      "grid-column": groupColumns ? `span ${span}` : "span 1",
      "grid-row": controlType != "select" ? "span 4" : "span 1",
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
    tall={controlType != "select"}
    height={$component.styles.normal?.height || "15rem"}
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
        {inBuilder}
        tableid={formContext?.dataSource?.tableId}
        on:change={(e) => handleChange(e.detail)}
      />
    {:else if controlType == "slider"}
      <CellAttachmentSlider
        {cellOptions}
        {value}
        {fieldSchema}
        {autofocus}
        {API}
        {inBuilder}
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
        {inBuilder}
        tableid={formContext?.dataSource?.tableId}
        on:change={(e) => handleChange(e.detail)}
      />
    {/if}
  </SuperField>
</div>
