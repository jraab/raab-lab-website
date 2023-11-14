---
# Leave the homepage title empty to use the site title
title:
date: 2022-10-24
type: landing

sections:
  - block: hero
    content: 
      title:  | 
        The **Raab Lab** at UNC Chapel Hill
      
  - block: markdown
    content: 
      title: 
      subtitle: 
      text: 
      design: 
        columns: 1
        background: 
          image: 
            filename: coders.jpg
            filters:
              brightness: 1
            parallax: false
            position: center
            size: cover
            text_color_light: true
        spacing: 
          padding: ['20px', '0', '20px', '0']
        css_class: fullscreen
          
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

 