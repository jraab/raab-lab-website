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
      design: 
        css_class: fullscreen

  - block: markdown 
    content: 
      title: 
      subtitle: 
      text: Testing out the possibility of using this as a landing page 
      image: 
        filename: raablab.jpg
      design:  
        view: showcase
        columns: 1

  - block: markdown 
    id: project_2  
    content: 
      text: This is made up project 2 
      image: 
        filename: HE_CKO_liver.jpeg
      design: 
        view: showcase
        columns: 1

  - block: markdown
    content:
      title:
      subtitle: ''
      text: This is project 3
    design:
      columns: '1'
      background:
        image: 
          filename: HE_CKO_liver.jpg
          filters:
            brightness: 1
          parallax: false
          position: center
          size: cover
          text_color_light: true
      spacing:
        padding: ['20px', '0', '20px', '0']
      css_class: fullscreen


---

 