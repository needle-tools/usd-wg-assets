<script lang="ts">
import { base } from '$app/paths';
import GridItem from '../../GridItem.svelte';
import Breadcrumbs from '../../Breadcrumbs.svelte';
import { path } from '$lib/settings';
export let data;

function getViewerUrl(abs: string) {
    const encoded = encodeURIComponent(abs);
    return base + "/view/?file=" + encoded;
    // return "https://usd-viewer.glitch.me/?file=" + encoded;
}

$: absoluteGithubUrl = item ? "https://github.com/usd-wg/assets/blob/main/" + item.path + item.ext : "";
$: $path = data.slug + "/";
$: dir = (data.currentDir?.items?.length != 1 ? data.currentDir : null);
$: item = data.currentItem || (data.currentDir?.items.length == 1 ? data.currentDir.items[0] : null);
$: title = item ? (item.path.split("/").pop() + item.ext) : "USD Assets";
$: thumbnailPath = item ? "https://github.com/usd-wg/assets/blob/main" + "/" + item.src + "?raw=true" : "";

function getNewIssueUrl() {
    const repo = "https://github.com/needle-tools/usd-wg-assets/issues/new";
    
    if (!item) return repo;    
    if (typeof navigator === "undefined") return repo;

    const params = new URLSearchParams();
    params.append("body", `### Description\n<!-- Please describe the issue you're seeing so that it can be reproduced.-->\n\n### Asset Link\n${item.filename}\n${window.location.href}\n\n### System Info\n\`\`\`${navigator.userAgent}\`\`\``);
    params.append("title", `Issue with ${item.filename} at \`${item.path}\``);
    return repo + "?" + params.toString();
}

</script>

<svelte:head>
<title>{ title }</title>
<meta name="description" content="USD Assets" />
<meta property="og:title" content={ title } />
<meta property="og:description" content="USD Assets" />
<meta property="og:image" content={thumbnailPath} />
<meta property="og:url" content={absoluteGithubUrl} />
</svelte:head>

<div>

<!--
<a href={base}>See all assets</a><br/>
-->

{#if dir}
    <Breadcrumbs dir={dir} />
    <article class="grid">
        <GridItem child={dir} mode="grid"/>
    </article>
{:else if item}
    <Breadcrumbs dir={item} />
    <article>
        <div>
            {#if globalThis["window"] && window.crossOriginIsolated}

            <iframe title="USD Viewer" src={getViewerUrl(absoluteGithubUrl)} 
                sandbox="allow-scripts allow-same-origin allow-downloads" 
                allow="cross-origin-isolated">
            </iframe>

            {:else}
            
            <a href={getViewerUrl(absoluteGithubUrl)} target="_blank" class="viewer-link">
                <img src={base}/{item.src} alt="Automatically generated Thumbnail"/>  
                <span>Open in web-based USD Viewer</span>
            </a>

            {/if}

            <a href={getNewIssueUrl()} target="_blank">Report an issue</a><br/>
            <a href={absoluteGithubUrl} target="_blank">View {item.filename}{item.ext} on GitHub</a><br/>
            {#if data.usdText}
            <a href="#code">View USD code</a>
            {/if}
        </div>
    </article>
{:else}
<p>Something went wrong, please report a bug with this URL and where you came from. Thanks!</p>
{/if}

{@html data.readmeMarkdown}

<br/>
<a href={data.absoluteReadmeUrl} target="_blank">Edit this page</a>

{#if data.usdText && item}
<h2 id="code">{item.filename}{item.ext}</h2>
<code><pre>{@html data.usdText}</pre></code>
{/if}

</div>

<style>
article {
    display: flex;
    flex-direction: column;
    margin-bottom: 2em;
}

p {
    opacity: 0.5;
    font-size: 0.7em;
}

a.viewer-link {
    display: flex;
    flex-direction: column;
    align-items: start;
}

a.viewer-link img {
    border: 1px solid transparent;
    margin: 0.5em 0em;
}

a.viewer-link:hover img {
    background-color: white;
    border: 1px solid rgb(235, 235, 235);
}

article.grid {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(var(--image-size), 1fr));
    gap: 0.5em;
}

code {
    white-space: pre-wrap;
    font-size: 0.9em;
}

iframe {
    outline: 0;
    border: 0;
    width: 100%;
    height: 400px;
}

</style>