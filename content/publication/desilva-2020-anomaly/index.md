---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "Physics-informed machine learning for sensor fault detection with flight test data"
authors: ["Brian M. de Silva", "Jared Callaham", "Jonathan Jonker", "Nicholas Goebel", "Jennifer Klemisch", "Darren McDonald", "Nathan Hicks", "J. Nathan Kutz", "Steven L. Brunton", "Aleksandr Y. Aravkin"]
date: 2020-06-25T10:12:04-07:00
doi: ""

# Schedule page publish date (NOT publication's date).
publishDate: 2020-06-25T10:12:04-07:00

# Publication type.
# Legend: 0 = Uncategorized; 1 = Conference paper; 2 = Journal article;
# 3 = Preprint / Working Paper; 4 = Report; 5 = Book; 6 = Book section;
# 7 = Thesis; 8 = Patent
publication_types: ["3"]

# Publication name and optional abbreviated publication name.
publication: "Submitted to *AIAA*"
publication_short: ""

abstract: "We develop data-driven algorithms to fully automate sensor fault detection in systems governed by underlying physics. The proposed machine learning method uses a time series of typical behavior to approximate the evolution of measurements of interest by a linear time-invariant system. Given additional data from related sensors, a Kalman observer is used to maintain a separate real-time estimate of the measurement of interest. Sustained deviation between the measurements and the estimate is used to detect anomalous behavior. A decision tree, informed by integrating other sensor measurement values, is used to determine the amount of deviation required to identify a sensor fault. We validate the method by applying it to three test systems exhibiting various types of sensor faults: commercial flight test data, an unsteady aerodynamics model with dynamic stall, and a model for longitudinal flight dynamics forced by atmospheric turbulence. In the latter two cases we test fault detection for several prototypical failure modes. The combination of a learned dynamical model with the automated decision tree accurately detects sensor faults in each case."

# Summary. An optional shortened abstract.
summary: ""

tags: []
categories: []
featured: true

# Custom links (optional).
#   Uncomment and edit lines below to show custom links.
# links:
# - name: Follow
#   url: https://twitter.com
#   icon_pack: fab
#   icon: twitter

url_pdf: "https://arxiv.org/pdf/2006.13380.pdf"
url_code:
url_dataset:
url_poster:
url_project:
url_slides:
url_source:
url_video:

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder. 
# Focal points: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight.
image:
  caption: ""
  focal_point: ""
  preview_only: false

# Associated Projects (optional).
#   Associate this publication with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `internal-project` references `content/project/internal-project/index.md`.
#   Otherwise, set `projects: []`.
projects: []

# Slides (optional).
#   Associate this publication with Markdown slides.
#   Simply enter your slide deck's filename without extension.
#   E.g. `slides: "example"` references `content/slides/example/index.md`.
#   Otherwise, set `slides: ""`.
slides: ""
---
