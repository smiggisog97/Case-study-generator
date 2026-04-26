A Claude Code slash command (/muhidcasestudy) that turns raw product documentation into a production-ready case study page.                          
   
  Input: PRD, BRD, designer story, project metadata (company, team, timeline, platform)                                                                
           
  Output: A complete React component with:                                                                                                             
  - 15-section scroll-driven layout (Hero → Impact → Overview → Context → Problem → Process → Research → Exploration → Before/After → Decisions → Final
   Design → Callout → Impact → Reflections → Footer)                                                                                                   
  - Granular reveal animations — every element animates individually as it enters the viewport, not whole sections at once
  - SplitWords hero headline where each word slides up independently                                                                                   
  - PDF export via window.print() with a style override that forces animated elements visible                                                          
  - Image placeholders with exact dimensions so the user can swap in real assets later                                                                 
  - All data extracted to const arrays at the top — no inline data in JSX                                                                              
                                                                                                                                                       
  Stack: React 19 + Vite + Tailwind CSS v4 + Framer Motion                                                                                             
                                                                                                                                                       
  Install: Drop muhidcasestudy.md into ~/.claude/commands/ and run /muhidcasestudy in any Claude Code session.                                         
                                         
  Built for product designers who want a high-quality case study without starting from scratch.   
