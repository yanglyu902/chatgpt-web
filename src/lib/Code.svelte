<script lang="ts">
  import { Highlight } from 'svelte-highlight'
  import flourite from 'flourite'

  // Import both dark and light styles
  import { github, githubDark, atomOneDark } from 'svelte-highlight/styles'

  // Style depends on system theme
  const style = window.matchMedia('(prefers-color-scheme: dark)').matches ? atomOneDark : github

  // Copy function for the code block
  import copy from 'copy-to-clipboard'

  // Import all supported languages
  import {
    javascript,
    python,
    typescript,
    ruby,
    go,
    java,
    sql,
    shell,
    php,
    plaintext,
    yaml,
    type LanguageType
  } from 'svelte-highlight/languages'

  export const type: 'code' = 'code'
  export const raw: string = ''
  export const codeBlockStyle: 'indented' | undefined = undefined
  export let lang: string | undefined
  export let text: string

  // Map lang string to LanguageType
  let language: LanguageType<string>

  // If no language is set, try to detect it using flourite
  if (!lang) {
    lang = flourite(text, { shiki: true }).language
  }

  switch (lang) {
    case 'js':
    case 'javascript':
      language = javascript
      break
    case 'py':
    case 'python':
      language = python
      break
    case 'ts':
    case 'typescript':
      language = typescript
      break
    case 'rb':
    case 'ruby':
      language = ruby
      break
    case 'go':
    case 'golang':
      language = go
      break
    case 'java':
      language = java
      break
    case 'sql':
      language = sql
      break
    case 'sh':
    case 'shell':
    case 'bash':
    case 'console':
    case 'shellscript':
    case 'zsh':
      language = shell
      break
    case 'php':
      language = php
      break
    case 'yaml':
    case 'yml':
      language = yaml
      break
    default:
      language = python;
  }

  // For copying code - reference: https://vyacheslavbasharov.com/blog/adding-click-to-copy-code-markdown-blog
  const copyFunction = (event) => {
    // Get the button the user clicked on
    const clickedElement = event.target as HTMLButtonElement

    // Get the next element
    const nextElement = clickedElement.nextElementSibling as HTMLElement

    // Modify the appearance of the button
    const originalButtonContent = clickedElement.innerHTML
    clickedElement.classList.add('is-success')
    clickedElement.innerHTML = 'Copied!'

    // Retrieve the code in the code block
    const codeBlock = (nextElement.querySelector('pre > code') as HTMLPreElement).innerText
    copy(codeBlock)

    // Restored the button after copying the text in 2 second.
    setTimeout(() => {
      clickedElement.innerHTML = originalButtonContent;
      clickedElement.classList.remove("is-success");
      clickedElement.blur();
    }, 2000);
  };
</script>

<svelte:head>
  {@html style}
</svelte:head>

<div class="code-block is-relative">
  <button class="button is-light is-outlined is-small p-2" on:click={copyFunction}>Copy</button>
  <Highlight code={text} language={language} />

    <!-- <Highlight language={language} code={text} let:highlighted>
        <LineNumbers {highlighted} hideBorder={true}
        --padding-left="1em"
        --padding-right="1em"
        />
    </Highlight> -->


    <!-- <Highlight language={language} code={text} let:highlighted>
        <div style="--lh: ;">
          <pre>
            <LineNumbers {highlighted} hideBorder={true} style="--padding-left: 0.5em; --padding-right: 0.5em; --background-color: #f7f7f7;" />
            {highlighted}
          </pre>
        </div>
      </Highlight> -->
</div>
