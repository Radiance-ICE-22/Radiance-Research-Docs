## Philosophy 
1. Code reviews are for knowledge sharing and will be done through pull requests. Everyone is encouraged to actively participate in code reviews as a mean of knowledge sharing. 
2. Time and attention are scarce resources, thus every pull request will be in draft state until a major contribution is done; and only valuable information would go into the documentation.
3. We won't allow rotten pull requests thus after required approvals are reached, pull requests will be merged within 12 hours.
4. All the changes (unless it doesn't involve with knowledge content) will be done through pull requests.
5. All the pull requests require to pass the CI/CD check before getting merged.
6. Make sure all the necessary metadata are correct and complete.

## Workflow

> [!IMPORTANT]
> Open `/content/` as your value in Obsidan


> [!IMPORTANT]
> Keep the attachment settings like this for consistency : **Files and Links** -> **Default location for new attachments** -> **In a subfolder under current folder**

1. Run the tests locally with `npm test`
2. Test bulid and check the website with `npx quartz build --serve`
3. Finally push to your seperate branch using `npx quartz sync` or use regular `git push` with a meaningful commit message.

## Plugin Usage
> [!WARNING]
> Since not all the plugins are natively supported by quartz, it's not encouraged customize the plugins or installing the plugins considering it's a multi collaborator repository.

- References are managed through [Zotero](https://github.com/zotero/zotero) and a new note for paper can be initiated throught `Zotero Integration` plugin. Using **command pallet**(`ctrl + p`) and search **Zotero Integration: New Paper** which instantiates a predefined template instance for paper.
- The **Question & Answers** section can be used to generate **Anki flashcard**. If needed setup the Anki using this [guide](https://github.com/ObsidianToAnki/Obsidian_to_Anki?tab=readme-ov-file#setup)
- Whenever using **Excalidraw** for drawing, make sure to embedded the drawing in the notes using **Excalidraw: Embed a drawing**, and it automatically embeddes the `svg` file and links to the respective `excalidraw.md` file. 
