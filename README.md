Screenplay adaptation of "Friendship is Optimal" (Equestria Online prologue) following the AI video/comic book generation workflow defined in [AGENTS.md](./AGENTS.md).

The goal of this project is to create `mypi` agents ("stan-lee", etc) and skills which produce an end-to-end workflow that takes an origional story and produces a comic book.  This multi-step process (found in [AGENTS.md](./AGENTS.md)) takes the origional story and converts into a screenplay format along with a collection of character, scene and prop image prompt descriptions then uses this to convert into multi-panel page prompts which collate the 



---


1. find and tidy an origional manuscript

Each chapter should be cleaned markdown/txt files and seperated into chapters.

./story/origional/*.md

2. Named entity extraction (characters, locations, props)

./prompts/{characters,locations,styles}/*.md

We go through the story and collate all the descriptions along the way, as well as their variations through the story

These shouldn't be in the format of an image prompt but instead be similar descriptive wording from the prose - we extract quotes and re-write for image prompt usage (as in, we might 'fill in the blanks' artistically similarly to how the human mind will image its own version of "dark cave" but not take too much freedom which will depart from the author's intent).  We need to use references to parts in the story (chapter/verse-ish) for future reference - for example, if a character has a variation with a wound, implant, under-the-influcence, in a costume, etc.

3. Convert narrative flow into screenplay format

