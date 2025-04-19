Approach to Designing Systems:
   - Ask clarifying questions
   - Do frequent check in's throughout the process
   - Leverage Collaboration with the Interviewer
   - If you get stuck, don't hesitate to ask for hints
   - Wait for the interviewer to acknowledge, prompts or guides you to the next point or phase
   
1)  Establish Design Scope: 5 - 10 mins
   - Functional Requirements:
     - What features are we building? 
     - Suggest some features if it is a common system like Whatsapp, Twitter etc;
     - Web vs Mobile
   - Assumption
     - How many Users does the product have
     - Scale Up/Growth characteristics:
       - Number of Users in 3 months, 6 months & 1 year
       - Daily Active Usage
       - What data types are we going to handle: messages, files, video?
   - Integration
     - Current Technology Stack
     - Existing Services or Technology We may leverage

2) Design the High Level: 10 - 15 mins
    - Visualize the System Diagram -or- Design outline of the system if white boarding
    - Identify the core components using boxes
    - Suggest multiple approaches if possible
    - Identify connections between components
    - Do back of the envelope calculations to evaluate scale constraints
    - Validate/Walk through a User Journey or data flow
    - Discover any edge cases

3) Design Deep dive: 15 -25 mins
   - By this time you have established scope & agreed on high level design
   - Dig into Details:
     - Table
     - API's
     - System Design problem specific deep dives:
       - Algorithms 
       - Hash Functions (ex: tinyUrl)
       - Reducing Latency (ex: news feed)
     - Does any use case/feature require a walkthrough with more low level discussion like:
       - Sequence Diagram
     - How does your implementation handle:
       - Scalability
       - Resilience
       - CAP trade off
      
4) Wrap Up:  5 - 10 mins
   - Ask follow-up questions to get direction
   - Identify Bottle Necks
     - Error Handling
     - Operational Issues
     - Observability: Monitor, Metrics and Error logs
   - Handling Next Scale curve? go from 100K to 1 million users
   - Propose Refinements or Alternate approaches