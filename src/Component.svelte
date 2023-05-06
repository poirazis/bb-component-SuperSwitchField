<script>
  import { getContext, onDestroy } from "svelte"

  const { styleable } = getContext("sdk")
  const component = getContext("component")

  const formContext = getContext("form");
  const formStepContext = getContext("form-step");  
  const fieldGroup = getContext("field-group")
  const formApi = formContext?.formApi;

  export let label
  export let disabled
  export let field
  export let defaultValue
  export let emphasized
  export let size = "M"

  let formField
  let formStep
  let fieldState
  let fieldApi
  let value
  let labelPos = fieldGroup?.labelPos || "left" 

  $: formStep = formStepContext ? $formStepContext || 1 : 1;
  $: formField = formApi?.registerField(field, "boolean", 0, false, null, formStep);
  $: value = fieldState?.value || defaultValue
  $: disabled = disabled || fieldState?.disabled

  $: unsubscribe = formField?.subscribe((value) => {
    fieldState = value?.fieldState;
    fieldApi = value?.fieldApi;
  });


  function handleClick( event ) {
    value = !value
    fieldApi.setValue(value)
  }
``
  onDestroy(() => {
    fieldApi?.deregister();
    unsubscribe?.();  
  });

  $: console.log(label, emphasized)
</script>

<div class="spectrum-Form-item" use:styleable={$component.styles}>

  {#if !formContext}
    <div class="placeholder">Form components need to be wrapped in a form</div>
  {:else if !field}
    <div class="welcome"> Please Select a Field to get Started </div>
  {:else if fieldGroup}
    <label class="spectrum-FieldLabel spectrum-FieldLabel--sizeM spectrum-Form-itemLabel spectrum-FieldLabel--{labelPos}" for={fieldState?.fieldId}>{ label }</label>
    <div class="spectrum-Form-itemField">
      <!-- svelte-ignore a11y-click-events-have-key-events -->
      <div on:click={handleClick} class:spectrum-Switch--emphasized={emphasized} class="spectrum-Switch spectrum-Switch--size{size}" >
        <input id={fieldState?.fieldId} type="checkbox" class="spectrum-Switch-input" checked={value} disabled={disabled}>
          <span class="spectrum-Switch-switch"></span>
      </div>
    </div>
  {:else}
   
    <div class="spectrum-Form-itemField" >
      <!-- svelte-ignore a11y-click-events-have-key-events -->
      <div on:click={handleClick}  class:spectrum-Switch--emphasized={emphasized} class="spectrum-Switch spectrum-Switch--size{size}" >
        <input id={fieldState?.fieldId} type="checkbox" class="spectrum-Switch-input" checked={value} disabled={disabled}>
          <span class="spectrum-Switch-switch"></span>
          <label
            style:margin-left={"0.85rem"}
            class="spectrum-FieldLabel spectrum-FieldLabel--sizeM spectrum-Form-itemLabel" 
            for={fieldState?.fieldId}>{ label }</label>
      </div>
      
    </div>
  {/if}

</div>

<style>
  .spectrum-Switch {
    align-items: center !important;
  }

</style>