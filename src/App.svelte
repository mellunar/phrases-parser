<script>
  import { t } from "svelte-i18n";
  import { Router } from "@roxi/routify";
  import { routes } from "../.routify/routes";
  import "./app.scss";
</script>

<Router {routes} />
