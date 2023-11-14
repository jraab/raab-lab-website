---
# Leave the homepage title empty to use the site title
title:
date: 2022-10-24
type: landing

sections:
  - block: hero
    content: 
      title:  | 
        The **Raab Lab** 
      text: We are in the Department of Genetics at UNC Chapel Hill. We study how chromatin mediated gene regulation is controlled and how disruption of chromatin contributes to cancer and other diseases

  - block: markdown
    content:
      title:
      subtitle: ''
      text:
    design:
      columns: '1'
      background:
        image: 
          filename: 'cool_image.jpg' 
          filters:
            brightness: 1
          parallax: false
          position: center
          text_color_light: true
     
  - block: portfolio
    id: Research
    title: Projects
    text: 
    content: 
      title: 
      filters: 
        folders: 
          - projects
      sort_by: Params.weight # need to put them in reverse for some odd reason
    design: 
      columns: '1'
      view: showcase
      flip_alt_rows: false

---

 