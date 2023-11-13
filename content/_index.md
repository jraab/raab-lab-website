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
      image: 
        filename: raablab.jpg    

  - block: hero
    content:  
      title: 
      text: Learn mor about our Research
      banner: 
        image: 'cool_image.jpg'
  - block: portfolio
    id: projects
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

 