<!-- Footer.svelte -->
<footer>
    <div class="footer-content">
      <p>© 2025 My Website. All rights reserved.</p>
      <div class="social-links">
        <a href="https://twitter.com" target="_blank" rel="noopener noreferrer">Twitter</a>
        <a href="https://facebook.com" target="_blank" rel="noopener noreferrer">Facebook</a>
        <a href="https://instagram.com" target="_blank" rel="noopener noreferrer">Instagram</a>
      </div>
    </div>
  </footer>
  
  <style>
  .footer {
    position: fixed;
    bottom: 0;
    left: 0;
    width: 100%;
    background-color: rgba(14, 14, 14, 0.95);
    color: white;
    text-align: center;
    padding: 1rem 0;
    z-index: 1000;
  }
  
    .footer-content {
      max-width: 1200px;
      margin: 0 auto;
    }
  
    .footer-content p {
      margin: 0;
    }
  
    .social-links {
      margin-top: 1rem;
    }
  
    .social-links a {
      color: white;
      text-decoration: none;
      margin: 0 1rem;
      font-size: 1.2rem;
    }
  
    .social-links a:hover {
      text-decoration: underline;
    }
  </style>
  