const original = $json; // keep entire Airtable item

const fields = original.fields || {};

const rawJob =
  fields["Job Type"] ??
  fields["Job type"] ??
  fields["job_type"] ??
  "job";

let jobString = rawJob;
if (Array.isArray(jobString)) jobString = jobString[0] || "job";

const slug = String(jobString)
  .toLowerCase()
  .replace(/[^a-z0-9]+/g, "_")
  .replace(/^_+|_+$/g, "") || "job";

const recordId = original.id || "rec_unknown";
const created = new Date(original.createdTime || Date.now());

const pad = (n) => String(n).padStart(2, "0");
const ts = `${created.getFullYear()}${pad(created.getMonth()+1)}${pad(created.getDate())}-${pad(created.getHours())}${pad(created.getMinutes())}`;

const runId = `${ts}-${slug}-${recordId}`;
const repoPath = `agent_os/runs/${runId}`;

const lockToken = `${$execution.id}-${recordId}`;
const lockExpires = new Date(Date.now() + 10 * 60 * 1000).toISOString();

return [{
  ...original,            // 🔥 preserve everything
  runId,
  repoPath,
  jobTypeSlug: slug,
  lockToken,
  lockExpires
}];
