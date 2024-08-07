<script>
  import { getContext, onDestroy } from "svelte";
  import CellAttachment from "../../bb_super_components_shared/src/lib/SuperTableCells/CellAttachment.svelte";
  import SuperButton from "../../bb_super_components_shared/src/lib/SuperButton/SuperButton.svelte";
  import "../../bb_super_components_shared/src/lib/SuperFieldsCommon.css";

  const { styleable, enrichButtonActions } = getContext("sdk");
  const component = getContext("component");
  const allContext = getContext("context");

  const formContext = getContext("form");
  const formStepContext = getContext("form-step");
  const groupLabelPosition = getContext("field-group");
  const labelWidth = getContext("field-group-label-width");
  const groupDisabled = getContext("field-group-disabled");
  const formApi = formContext?.formApi;

  export let field;

  export let customButtons;

  export let buttons = [];
  export let buttonsQuiet;

  export let label;
  export let span = 6;
  export let placeholder;
  export let defaultValue;
  export let template;
  export let disabled;
  export let readonly;
  export let validation;

  export let onChange;
  export let debounced;
  export let debounceDelay;
  export let autofocus;

  export let icon;
  export let suggestions;
  export let clearValueIcon;

  export let role;
  export let labelPosition;

  let formField;
  let formStep;
  let fieldState;
  let fieldApi;
  let fieldSchema;
  let value;
  let cellState;

  $: formStep = formStepContext ? $formStepContext || 1 : 1;
  $: labelPos = labelPosition ? labelPosition : groupLabelPosition || "left";

  $: formField = formApi?.registerField(
    field,
    "string",
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
    error: fieldState.error,
    role,
  };

  $: $component.styles = {
    ...$component.styles,
    normal: {
      ...$component.styles.normal,
      "flex-direction": labelPos == "left" ? "row" : "column",
      "align-items": "stretch",
      gap: labelPos == "left" ? "0.5rem" : "0rem",
      "grid-column": labelPos ? "span " + span : "span 1",
      "--label-width":
        labelPos == "left" ? (labelWidth ? labelWidth : "6rem") : "auto",
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
<div class="superField" use:styleable={$component.styles}>
  {#if label}
    <label
      for="superCell"
      class="superlabel"
      class:left={labelPos == "left"}
      on:mousedown|preventDefault={cellState.focus}
    >
      {label}
      {#if fieldState.error}
        <div class="error" class:left={labelPos == "left"}>
          <span>{fieldState.error}</span>
        </div>
      {/if}
    </label>
  {/if}

  <div class="inline-cells">
    <CellAttachment
      bind:cellState
      {cellOptions}
      {value}
      {fieldSchema}
      {autofocus}
      on:change={(e) => handleChange(e.detail)}
    />
    {#if customButtons && buttons?.length}
      <div
        class="spectrum-ActionGroup spectrum-ActionGroup--compact spectrum-ActionGroup--sizeM"
        class:spectrum-ActionGroup--quiet={buttonsQuiet}
      >
        {#each buttons as { text, onClick, quiet, disabled, type }}
          <SuperButton
            quiet={buttonsQuiet || quiet}
            {disabled}
            size="M"
            {text}
            onClick={enrichButtonActions(onClick, $allContext)}
            emphasized
            selected={type == "cta"}
          />
        {/each}
      </div>
    {/if}
  </div>
</div>
