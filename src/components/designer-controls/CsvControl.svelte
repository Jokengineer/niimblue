<script lang="ts">
  import { tr } from "$/utils/i18n";
  import { csvParse, csvFormat } from "d3-dsv";
  import MdIcon from "$/components/basic/MdIcon.svelte";
  import HelpButton from "$/components/basic/HelpButton.svelte";
  import CsvHelpAnimation from "$/components/basic/CsvHelpAnimation.svelte";
  import ToggleHelpAnimation from "$/components/basic/ToggleHelpAnimation.svelte";
  import { csvData } from "$/stores";

  interface Props {
    enabled: boolean;
    onPlaceholderPicked: (name: string) => void;
    onOpenPreview: () => void;
  }

  let { enabled = $bindable(), onPlaceholderPicked, onOpenPreview }: Props = $props();

  type Variable = { id: number; name: string; valuesText: string };

  let nextId = 0;

  /** One value per line; a trailing blank line is ignored. */
  const splitValues = (text: string): string[] => {
    if (text.trim() === "") return [];
    return text.replace(/\n+$/, "").split("\n");
  };

  /** Build the editable form model from the stored CSV string. */
  const csvToVariables = (csv: string): Variable[] => {
    try {
      const parsed = csvParse(csv);
      const cols = parsed.columns ?? [];
      if (cols.length === 0) {
        return [{ id: nextId++, name: "text", valuesText: "" }];
      }
      return cols.map((c) => ({
        id: nextId++,
        name: c,
        valuesText: parsed.map((r) => r[c] ?? "").join("\n"),
      }));
    } catch {
      return [{ id: nextId++, name: "text", valuesText: "" }];
    }
  };

  let variables = $state<Variable[]>(csvToVariables($csvData.data));

  const names = $derived(variables.map((v) => v.name.trim()).filter((n) => n !== ""));
  const labelCount = $derived(
    variables.reduce((max, v) => Math.max(max, splitValues(v.valuesText).length), 0),
  );

  /** Write the form back into the stored CSV string the print engine reads. */
  const serialize = () => {
    const activeVars = variables.filter((v) => v.name.trim() !== "");
    if (activeVars.length === 0) {
      csvData.set({ data: "" });
      return;
    }
    const cols = activeVars.map((v) => v.name.trim());
    const rows: Record<string, string>[] = [];
    for (let i = 0; i < labelCount; i++) {
      const row: Record<string, string> = {};
      for (const v of activeVars) {
        row[v.name.trim()] = splitValues(v.valuesText)[i] ?? "";
      }
      rows.push(row);
    }
    csvData.set({ data: csvFormat(rows, cols) });
  };

  $effect(() => {
    serialize();
  });

  const touched = () => {
    enabled = true;
  };

  const addVariable = () => {
    variables = [...variables, { id: nextId++, name: "", valuesText: "" }];
    enabled = true;
  };

  const removeVariable = (id: number) => {
    variables = variables.filter((v) => v.id !== id);
    if (variables.length === 0) {
      variables = [{ id: nextId++, name: "text", valuesText: "" }];
    }
    enabled = true;
  };
</script>

<div class="dropdown">
  <button
    class="btn btn-sm btn-{enabled ? 'warning' : 'secondary'}"
    data-bs-toggle="dropdown"
    data-bs-auto-close="outside"
    title={$tr("params.csv.title")}>
    <MdIcon icon="dataset" />
  </button>
  <div class="dropdown-menu">
    <div class="dropdown-header d-flex align-items-center gap-2">
      <span class="flex-grow-1">{$tr("params.csv.title")}</span>
      <HelpButton>
        <CsvHelpAnimation />
        <div class="help-intro">{$tr("params.csv.help.intro")}</div>
        <ol class="help-steps">
          <li>{$tr("params.csv.help.1")}</li>
          <li>{$tr("params.csv.help.2")}</li>
          <li>
            {$tr("params.csv.help.preview")}
            <div class="mt-1">
              <button type="button" class="btn btn-sm btn-primary" onclick={() => onOpenPreview()}>
                <MdIcon icon="visibility" /> {$tr("editor.preview")}
              </button>
            </div>
          </li>
          <li>
            {$tr("params.csv.help.toggle")}
            <ToggleHelpAnimation />
          </li>
        </ol>
      </HelpButton>
    </div>

    <div class="p-3 text-body-secondary">
      <div class="form-check form-switch">
        <input class="form-check-input" type="checkbox" role="switch" id="csv-enabled" bind:checked={enabled} />
        <label class="form-check-label" for="csv-enabled">{$tr("params.csv.enabled")}</label>
      </div>

      <div class="mt-2 small">{$tr("params.csv.tip")}</div>

      {#each variables as v (v.id)}
        <div class="var-card mt-3">
          <div class="input-group input-group-sm mb-2">
            <span class="input-group-text">{$tr("params.csv.var.name")}</span>
            <input class="form-control" type="text" bind:value={v.name} oninput={touched} />
            {#if v.name.trim() !== ""}
              <span class="input-group-text metka">{`{${v.name.trim()}}`}</span>
            {/if}
            <button
              type="button"
              class="btn btn-outline-danger"
              title={$tr("params.csv.var.remove")}
              onclick={() => removeVariable(v.id)}>
              <MdIcon icon="delete" />
            </button>
          </div>
          <label class="form-label small mb-1" for="csv-vals-{v.id}">{$tr("params.csv.var.values")}</label>
          <textarea
            id="csv-vals-{v.id}"
            class="values-field form-control"
            bind:value={v.valuesText}
            oninput={touched}></textarea>
        </div>
      {/each}

      <button type="button" class="btn btn-sm btn-outline-secondary mt-2" onclick={addVariable}>
        <MdIcon icon="add" /> {$tr("params.csv.var.add")}
      </button>

      <div class="mt-3">
        {$tr("params.csv.labelcount")} <strong>{labelCount}</strong>
      </div>

      <div class="mt-2 placeholders">
        {$tr("params.csv.insert")}
        {#each names as n (n)}
          <button type="button" class="btn btn-sm btn-outline-info px-1 py-0" onclick={() => onPlaceholderPicked(n)}
            >{`{${n}}`}</button>
        {/each}
      </div>
    </div>
  </div>
</div>

<style>
  .dropdown-menu {
    width: 100vw;
    max-width: 380px;
  }
  .var-card {
    border: 1px solid rgba(128, 128, 128, 0.3);
    border-radius: 6px;
    padding: 10px;
  }
  .metka {
    font-family: monospace;
  }
  textarea.values-field {
    font-family: monospace;
    min-height: 120px;
    background-color: #ced4da;
    color: #212529;
  }
  textarea.values-field:focus {
    background-color: #ced4da;
    color: #212529;
    box-shadow: 0 0 0 0.2rem rgba(77, 171, 247, 0.35);
  }
  .help-intro {
    margin-bottom: 6px;
  }
  .help-steps {
    margin: 0;
    padding-left: 1.2em;
  }
  .help-steps li {
    margin-bottom: 10px;
  }
  textarea.values-field::placeholder {
    color: #6c757d;
  }
</style>
