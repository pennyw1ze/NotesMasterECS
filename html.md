<!DOCTYPE html>
<html lang="it">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Leonardo Rufini</title>
  <!-- Font da Google -->
  <link href="https://fonts.googleapis.com/css2?family=Roboto+Mono:wght@400;500;700&family=Poppins:wght@400;600;700&display=swap" rel="stylesheet">
  <!-- Font Awesome for Icons -->
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css">


  <!-- Inline CSS -->
  <style>
    /* New Deep Dark Theme CSS Variables */
    :root {
      /* New Color Palette */
      --primary-bg: #000814ff; /* rich-black */
      --secondary-bg: #001d3dff; /* oxford-blue */
      --card-bg: #002b54; /* Derived from oxford-blue, slightly lighter for distinction */
      --accent-color: #ffc300ff; /* mikado-yellow */
      --accent-dark: #ffd60aff; /* gold */
      --accent-deep: #cc9c00; /* A darker shade of mikado-yellow for contrast */
      --text-light: #e0e0e0ff; /* Lighter grey for better contrast with new darks */
      --text-muted: #b0b0b0ff; /* Muted grey */
      --text-white: #ffffffff; /* white */
      --border-color: rgba(255, 255, 255, 0.1); /* Slightly more visible border */
      --box-shadow: 0 10px 30px rgba(0, 0, 0, 0.7); /* Deeper, more intense shadow */
      --box-shadow-hover: 0 15px 40px rgba(0, 0, 0, 0.9);
      --border-radius-main: 12px;
      --border-radius-card: 8px;
      --transition-speed: 0.2s; /* Faster transition for hover responsiveness */
      --animation-duration: 0.7s; /* Slightly slower for a smoother fade/slide */
      --stagger-delay: 0.08s; /* Slightly reduced stagger for faster overall animation */
    }

    * {
      box-sizing: border-box;
    }

    html, body {
      overflow-x: hidden;
      margin: 0;
      scroll-behavior: smooth;
    }

    body {
      font-family: 'Poppins', sans-serif;
      margin: 0;
      line-height: 1.8;
      background: var(--primary-bg);
      color: var(--text-light);
    }
    
    /* Header e Navbar */
    header {
      background: var(--secondary-bg); /* Use deepest dark */
      color: var(--text-light);
      padding: 1.5em 0; /* Adjusted padding */
      text-align: center;
      box-shadow: var(--box-shadow);
      position: sticky;
      top: 0;
      z-index: 1000;
      transition: background-color 0.4s ease, padding 0.3s ease;
      display: flex;
      flex-direction: column;
      align-items: center;
      border-bottom: 1px solid var(--border-color); /* Subtle border */
    }

    /* Header scroll effect */
    header.scrolled {
      background-color: rgba(0, 8, 20, 0.95); /* Semi-transparent deepest dark when scrolled */
      padding: 0.8em 0;
    }
    
    header h1 {
      font-family: 'Roboto Mono', monospace;
      font-size: 3.4em; /* Even larger, more impactful title */
      margin: 0.2em 0 0.5em 0;
      letter-spacing: 2px;
      color: var(--accent-color);
      text-shadow: 0 0 15px rgba(255, 195, 0, 0.6); /* More prominent glow */
      transition: font-size 0.3s ease, margin 0.3s ease;
    }

    header.scrolled h1 {
        font-size: 2.6em;
    }

    nav {
      width: 100%;
      max-width: 1200px;
    }

    .nav-list {
      list-style: none;
      display: flex;
      justify-content: center;
      gap: 45px; /* More space */
      margin: 0;
      padding: 0;
      flex-wrap: wrap;
    }

    .nav-list li {
      margin: 0;
    }
    
    .nav-list a {
      color: var(--text-light);
      text-decoration: none;
      font-weight: 600;
      font-family: 'Poppins', sans-serif;
      font-size: 1.15em;
      transition: all var(--transition-speed) ease;
      padding: 8px 15px;
      border-radius: var(--border-radius-card);
      position: relative;
    }

    .nav-list a::after {
        content: '';
        position: absolute;
        width: 0;
        height: 2px;
        background: var(--accent-color);
        bottom: 0;
        left: 50%;
        transform: translateX(-50%);
        transition: width var(--transition-speed) ease;
    }

    .nav-list a:hover::after,
    .nav-list a.active::after {
        width: 100%;
    }

    .nav-list a:hover{
      color: var(--text-white); /* White on hover for strong contrast */
      transform: translateY(-3px);
      /* background-color: var(--accent-deep); Deeper accent background on hover */
      text-decoration: none;
    }

    /* Hamburger icon styling */
    .nav-toggle {
      display: none;
      background: none;
      border: none;
      color: var(--text-white); /* White icon for visibility */
      cursor: pointer;
      padding: 10px;
      z-index: 1001;
      transition: color var(--transition-speed) ease;
      position: relative; /* For the custom icon */
      width: 40px; /* Standard size */
      height: 40px;
      display: flex;
      align-items: center;
      justify-content: center;
    }
    .nav-toggle:hover {
        color: var(--accent-color);
    }
    /* Custom Hamburger Icon */
    .nav-toggle span {
      display: block;
      width: 30px;
      height: 3px;
      background: var(--text-white);
      border-radius: 3px;
      position: absolute;
      transition: all 0.3s ease;
    }
    .nav-toggle span:before,
    .nav-toggle span:after {
      content: '';
      position: absolute;
      width: 100%;
      height: 100%;
      background: var(--text-white);
      border-radius: 3px;
      transition: all 0.3s ease;
    }
    .nav-toggle span:before {
      transform: translateY(-10px);
    }
    .nav-toggle span:after {
      transform: translateY(10px);
    }

    /* Cross icon animation */
    .nav-toggle.active span {
      background: transparent;
    }
    .nav-toggle.active span:before {
      transform: translateY(0) rotate(45deg);
    }
    .nav-toggle.active span:after {
      transform: translateY(0) rotate(-45deg);
    }
    
    /* Sezioni */
    section {
      padding: 4.5em; /* More internal padding */
      max-width: 1000px;
      margin: 2.5em auto; /* Reduced margin between sections */
      background: var(--secondary-bg);
      box-shadow: var(--box-shadow);
      border-radius: var(--border-radius-main);
      color: var(--text-light);
      position: relative;
      border: 1px solid var(--border-color);

      opacity: 0;
      transform: translateY(40px); /* Deeper initial translateY */
      transition: opacity var(--animation-duration) ease-out, transform var(--animation-duration) ease-out;
    }

    section.is-visible {
      opacity: 1;
      transform: translateY(0);
    }

    section h2 {
      font-family: 'Roboto Mono', monospace;
      font-size: 2.8em; /* Larger section titles */
      color: var(--accent-color);
      margin-top: 0; /* Ensures consistent top spacing within section */
      margin-bottom: 0.8em;
      border-bottom: 2px solid var(--border-color);
      padding-bottom: 0.6em; /* Slightly more padding */
      text-shadow: 0 0 10px rgba(255, 195, 0, 0.5);
    }

    section p {
        font-size: 1.15em;
        margin-bottom: 1.2em;
        line-height: 1.9;
    }
    
    #project-list {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(320px, 1fr)); /* Slightly larger min-width */
      gap: 2.2em; /* More space between projects */
      margin-top: 2.5em;
    }

    .project {
      background: var(--card-bg);
      padding: 2.2em; /* More padding */
      border-radius: var(--border-radius-card);
      color: var(--text-light);
      box-shadow: 0 6px 20px rgba(0,0,0,0.4); /* Deeper initial shadow */
      transition: transform var(--transition-speed) ease-out, box-shadow var(--transition-speed) ease-out, opacity var(--animation-duration) ease-out;
      display: flex;
      flex-direction: column;
      justify-content: space-between;
      border: 1px solid var(--border-color);

      opacity: 0;
      transform: translateY(25px); /* Deeper initial translateY */
    }

    .project.is-visible {
        opacity: 1;
        transform: translateY(0);
    }

    .project:hover {
      transform: translateY(-10px) scale(1.03); /* More pronounced lift effect */
      box-shadow: var(--box-shadow-hover);
    }

    .project h3 {
        font-family: 'Poppins', sans-serif;
        font-size: 1.6em; /* Larger project title */
        margin-top: 0;
        margin-bottom: 0.6em;
        color: var(--accent-color);
        transition: color var(--transition-speed) ease;
    }

    .project h3:hover {
        color: var(--text-white); /* White on project title hover */
    }

    .project hr {
        border: none;
        border-top: 1px solid rgba(255, 255, 255, 0.15); /* Slightly more visible separator */
        margin: 1.2em 0;
    }

    .project p {
        font-size: 1.05em; /* Slightly larger project description */
        margin-bottom: 1.5em;
        flex-grow: 1;
        color: var(--text-muted); /* Muted text for description */
    }

    .project ul {
      list-style: none;
      padding: 0;
      margin-top: 1.8em; /* More space */
      font-size: 0.98em;
      color: var(--text-light);
      display: flex;
      flex-wrap: wrap;
      gap: 18px; /* More space between items */
    }

    .project ul li {
        background-color: rgba(0, 0, 0, 0.3); /* Darker background for labels */
        padding: 6px 12px;
        border-radius: 6px;
        font-family: 'Roboto Mono', monospace;
        color: var(--text-muted);
    }

    .project ul li strong {
        color: var(--accent-color);
    }
    
    /* Footer */
    footer {
      text-align: center;
      padding: 2.5em; /* More padding */
      background: var(--secondary-bg);
      color: var(--text-muted); /* Muted text for footer */
      width: 100%;
      margin-top: 3em; /* Reduced margin */
      box-shadow: 0 -8px 25px rgba(0, 0, 0, 0.7); /* Deeper shadow */
      border-top: 1px solid var(--border-color);
    }
    
    /* Link repository */
    .project a {
      text-decoration: none;
      color: var(--accent-color);
      transition: color var(--transition-speed) ease;
      font-weight: 700;
    }
    .project a:hover{
      color: var(--text-white); /* White on project link hover */
      text-decoration: none;
    }

    /* General Links */
    a{
      color: var(--accent-dark); /* Use a darker accent for general links */
      text-decoration: none;
      transition: color var(--transition-speed) ease;
    }
    a:hover {
        color: var(--accent-color); /* main accent on hover */
        text-decoration: none;
    }


    /* Every Section Links */
    section#about a,
    section#projects a,
    section#contact a {
        color: var(--accent-color);
        font-weight: 600;
        font-size: 1.15em;
    }

    section#about a:hover,
    section#projects a:hover,
    section#contact a:hover {
        color: var(--text-white);
    }

    /* BUTTON */
    .download-btn {
      display: inline-flex;
      align-items: center;
      padding: 16px 35px; /* Even larger button */
      background-color: var(--accent-color);
      color: var(--text-white);
      font-size: 19px;
      font-weight: 700;
      border: none;
      border-radius: var(--border-radius-main);
      text-decoration: none;
      transition: all var(--transition-speed) ease;
      box-shadow: var(--box-shadow);
      cursor: pointer;
      text-transform: uppercase;
      letter-spacing: 1.5px;
    }

    .download-btn:hover {
      background-color: var(--accent-dark); /* Darker accent on hover */
      transform: translateY(-6px) scale(1.04); /* More pronounced lift and scale */
      box-shadow: var(--box-shadow-hover);
    }

    .download-btn svg {
      margin-right: 14px;
      width: 26px; /* Larger icon */
      height: 26px;
      fill: var(--text-white);
    }

    /* Responsive adjustments */
    @media (max-width: 900px) {
        section {
            padding: 3.5em 2.5em;
            margin: 2.5em auto; /* Reduced margin */
        }
        header h1 {
            font-size: 2.8em;
        }
        section h2 {
            font-size: 2.4em;
        }
        .project {
            padding: 1.8em;
        }
        #project-list {
            gap: 1.8em;
        }
    }

    @media (max-width: 768px) {
      /* Ensure footer is visible and not affected by mobile nav overlay */
      body.nav-active {
        overflow: hidden; /* Prevent scrolling when mobile nav is open */
      }
      
      header {
          flex-direction: row;
          justify-content: space-between;
          padding: 1em 1.5em;
          align-items: center;
      }
      header h1 {
        font-size: 2.2em;
        margin: 0;
      }
      header.scrolled h1 {
        font-size: 2em;
      }

      .nav-toggle {
        display: flex; /* Changed from block to flex for centered icon */
      }

      nav {
        position: fixed;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%; /* Full viewport height */
        background: var(--primary-bg);
        display: flex;
        align-items: center;
        justify-content: center;
        transform: translateX(100%);
        transition: transform 0.4s ease-in-out;
        overflow-y: hidden; /* Prevent scrolling within the nav itself */
      }
      nav.active {
        transform: translateX(0);
      }

      .nav-list {
        flex-direction: column;
        gap: 25px;
        font-size: 1.5em;
        max-height: 100%; /* Ensure list doesn't exceed nav height */
        overflow-y: auto; /* Allow internal scrolling if content overflows */
        padding: 20px; /* Add some padding if it scrolls */
      }
      .nav-list a {
          font-size: 1.8em; /* Even larger links for better touch target */
          padding: 18px 30px;
          color: var(--text-white); /* White for mobile nav links */
      }
      .nav-list a:hover {
        background-color: var(--accent-deep);
        color: var(--text-white);
      }
      .nav-list a::after {
          height: 3px;
      }
      .nav-list a.active::after {
          background: var(--text-white); /* White underline for active mobile link */
      }


      section {
        padding: 2.5em 1.5em;
        margin: 2em auto; /* Reduced margin */
      }
      section h2 {
        font-size: 2em;
        padding-bottom: 0.5em;
      }
      section p {
        font-size: 1em;
      }
      #project-list {
        grid-template-columns: 1fr;
        gap: 1.5em;
      }
      .project {
        padding: 1.5em;
      }
      .project h3 {
        font-size: 1.4em;
      }
      .project p {
        font-size: 0.95em;
      }
      .project ul li {
        font-size: 0.9em;
        padding: 4px 8px;
      }
      .download-btn {
          padding: 14px 25px;
          font-size: 17px;
      }
      .download-btn svg {
          width: 22px;
          height: 22px;
      }
    }
  </style>
</head>
<body>
  <!-- Header e Navbar -->
  <header>
    <h1>Leonardo Rufini</h1>
    <button class="nav-toggle" aria-label="Toggle navigation">
      <!-- Replaced Font Awesome icon with custom spans for a cleaner animation -->
      <span></span>
    </button>
    <nav>
      <ul class="nav-list">
        <li><a href="#about">Who am I</a></li>
        <li><a href="#projects">Projects</a></li>
        <li><a href="#contact">Contacts</a></li>
        <li><a href="#curriculum">Curriculum</a></li>
      </ul>
    </nav>
  </header>

  <!-- Sezione Chi Sono -->
  <section id="about" class="animated-section">
    <h2>About me</h2>
    <p>Hi! I am Leonardo Rufini, an engineer in computer science who enjoys programming and loves technology.
      I graduated with a bachelor's degree in engineering in computer science at Sapienza University in Rome, 
      and I am now attending the Master's degree in Engineering in Computer Science. I love cryptography and attended the <a href="https://cyberchallenge.it" target="_blank" rel="noopener noreferrer">Cyberchallenge</a>
      with the Sapienza team in 2024.
      I am currently studying and enjoying advanced cryptography and blockchain, and trying to understand Zero Knowledge cryptographical techniques.
    </p>
  </section>

  <!-- Sezione Progetti -->
  <section id="projects" class="animated-section">
    <h2>Projects on Github</h2>
    <div id="project-list">
      <p style="color: var(--text-light);">Loading Github projects ...</p>
    </div>
  </section>

  <!-- Sezione Contatti -->
  <section id="contact" class="animated-section">
    <h2>Contact me</h2>
    <p>You can reach me via email at: <a href="mailto:leo.rufini.459@gmail.com">leo.rufini.459@gmail.com</a></p>
    <p>Follow me on <a href="https://github.com/pennyw1ze" target="_blank" rel="noopener noreferrer">GitHub</a> to keep up to date with my projects.</p>
  </section>

  <section id="curriculum" class="animated-section">
    <h2>Curriculum</h2>
    <p>Click the button to download my CV from Github.</p>
    <button type="button" class="download-btn" onclick="downloadCV()">
      <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24">
        <path d="M5 20h14v-2H5v2zm7-18l-5.5 5.5h4v6h3v-6h4L12 2z"/>
      </svg>
      Download CV
    </button>
    <script>
      function downloadCV() {
        const link = document.createElement('a');
        link.href = "https://raw.githubusercontent.com/leonardorufini/leonardorufini.github.io/main/CV.pdf";
        link.download = "CV.pdf";
        link.click();
      }
    </script> 
  </section>

  <!-- Footer -->
  <footer>
    <p>&copy; 2025 Leonardo Rufini</p>
  </footer>

  <!-- Script per caricare dinamicamente i progetti da GitHub e animazioni -->
  <script>
    document.addEventListener("DOMContentLoaded", function() {
      // --- Mobile Navigation Toggle ---
      const navToggle = document.querySelector('.nav-toggle');
      const nav = document.querySelector('nav');
      const navLinks = document.querySelectorAll('.nav-list a');
      const body = document.body; // Reference to the body element

      navToggle.addEventListener('click', () => {
        nav.classList.toggle('active');
        navToggle.classList.toggle('active'); // Toggle class on button for custom icon animation
        body.classList.toggle('nav-active'); // Toggle class on body to prevent scroll
      });

      navLinks.forEach(link => {
        link.addEventListener('click', () => {
          if (nav.classList.contains('active')) {
            nav.classList.remove('active');
            navToggle.classList.remove('active'); // Remove class from button
            body.classList.remove('nav-active'); // Remove class from body
          }
        });
      });


      // --- GitHub Projects Loading ---
      const apiURL = "https://api.github.com/users/pennyw1ze/repos";
      
      fetch(apiURL)
        .then(response => response.json())
        .then(data => {
          const projectList = document.getElementById("project-list");
          projectList.innerHTML = ""; // Clear the container
          
          data.sort((a, b) => new Date(b.updated_at) - new Date(a.updated_at));
          
          data.forEach(repo => {
            const projectDiv = document.createElement("div");
            projectDiv.className = "project";
            projectDiv.innerHTML = `
                <h3><a href="${repo.html_url}" target="_blank" rel="noopener noreferrer">${repo.name}</a></h3>
                <hr>
                <p>${repo.description ? repo.description : "No description provided."}</p>
                <ul>
                  <li><strong>Language:</strong> ${repo.language ? repo.language : "N/A"}</li>
                  <li><strong><i class="fas fa-star"></i> Stars:</strong> ${repo.stargazers_count}</li>
                  <li><strong><i class="fas fa-eye"></i> Watchers:</strong> ${repo.watchers_count}</li>
                </ul>
              `;
            projectList.appendChild(projectDiv);
          });

          // After projects are loaded, observe them for animations
          const projectCards = document.querySelectorAll('.project');
          projectCards.forEach((card, index) => {
              cardObserver.observe(card); // Use the new cardObserver
              // Removed the individual transitionDelay for consistent animation speed
              // card.style.transitionDelay = `${index * parseFloat(getComputedStyle(document.documentElement).getPropertyValue('--stagger-delay'))}s`; 
          });

        })
        .catch(error => {
          console.error("Errore durante il caricamento dei progetti:", error);
          document.getElementById("project-list").innerHTML = "<p style=\"color: var(--text-light);\">Failed to load projects from GitHub. Please try again later.</p>";
        });

      // --- Persistent Scroll Animations (Intersection Observer) ---
      const animatedSections = document.querySelectorAll('.animated-section');
      
      const observerOptions = {
        root: null, /* viewport */
        rootMargin: '0px',
        threshold: 0.1 /* Trigger when 10% of the element is visible */
      };

      const sectionObserver = new IntersectionObserver((entries) => {
        entries.forEach(entry => {
          if (entry.isIntersecting) {
            entry.target.classList.add('is-visible');
          } else {
            entry.target.classList.remove('is-visible'); // Remove class when out of view
          }
        });
      }, observerOptions);

      animatedSections.forEach(section => {
        sectionObserver.observe(section);
      });

      const cardObserver = new IntersectionObserver((entries) => {
        entries.forEach(entry => {
          if (entry.isIntersecting) {
            entry.target.classList.add('is-visible');
          } else {
            entry.target.classList.remove('is-visible');
          }
        });
      }, {
        root: null,
        rootMargin: '0px',
        threshold: 0.2 // Slightly higher threshold for cards
      });

      // --- Header Scroll Effect ---
      const header = document.querySelector('header');
      window.addEventListener('scroll', () => {
        if (window.scrollY > 50) {
          header.classList.add('scrolled');
        } else {
          header.classList.remove('scrolled');
        }
        updateActiveNavLink(); // Update active nav link on scroll
      });

      // --- Active Navigation Link on Scroll ---
      function updateActiveNavLink() {
        let currentActive = '';
        // Using Array.from to iterate over NodeList with forEach
        Array.from(animatedSections).reverse().forEach(section => { // Reverse to correctly pick top-most visible
          const sectionTop = section.offsetTop;
          // Offset by header height for better accuracy
          if (window.scrollY >= sectionTop - header.clientHeight - 30) { // Increased offset
            currentActive = section.getAttribute('id');
            return; // Exit loop once the first visible section (from bottom up) is found
          }
        });

        navLinks.forEach(a => {
          a.classList.remove('active');
          if (a.href.includes(currentActive) && currentActive !== '') { // Ensure currentActive is not empty
            a.classList.add('active');
          }
        });
      }

      // Initial call to set active link and handle potential immediate visibility
      updateActiveNavLink();
      // Ensure animations trigger for elements visible on page load
      animatedSections.forEach(section => {
        if (section.getBoundingClientRect().top < window.innerHeight && section.getBoundingClientRect().bottom > 0) {
          section.classList.add('is-visible');
        }
      });
    });
  </script>

</body>
</html>