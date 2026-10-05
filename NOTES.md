# Notes

## Summary of Changes
1. **Backend SQL Bug**: Fixed a critical operator precedence issue in `TaskRepository.java`, `search_tasks.sql`, and `task_search_package.sql`. The original query evaluated `AND` before `OR`, which incorrectly returned archived tasks if their descriptions matched the search term and caused status filters to be bypassed. I added parentheses to group the title and description conditions correctly.
2. **Frontend Race Condition**: The artificial delay on the backend caused out-of-order responses based on query length. I used an `AbortController` in `useTasks.js` and `api.js` to cancel stale in-flight requests on every keystroke, ensuring only the most recent request updates the UI.
3. **Pagination Bug**: Added logic in `App.jsx` to reset the page index back to 1 whenever the search query or status filter is modified. This prevents the user from being stranded on an empty page after filtering.
4. **UI Flickering**: Modified `TaskTable.jsx` to prevent the table from unmounting and replacing itself with a "Loading tasks..." div on every keystroke. Instead, the table remains visible but drops its opacity to `0.5` while fetching, vastly improving the UX.

## What I Chose Not to Change
I noticed that `TaskController.java` loads all matched results into memory (`List<Task> allResults`) before manually paginating via `.subList()`. I chose not to change this to true database-level pagination (using `Pageable`) because the in-memory H2 dataset is currently very small, and altering the repository return types would have expanded the scope beyond the allotted 90-minute patch window. 

## Biggest Remaining Risk
The most significant remaining risk is the lack of proper database-level pagination as mentioned above. If this application scales to thousands of tasks, fetching the entire result set into JVM memory on every keystroke will lead to severe performance degradation and potential `OutOfMemoryError` crashes.

## Tools and AI Used
I utilized an agentic AI assistant (Gemini 3.1 Pro) as an integrated pair programmer. I leveraged its capabilities to explore the repository structure, read code files to identify the logical flaws, and execute surgical modifications across the full stack (React + Spring Boot + SQL). The AI was responsible for applying the fixes directly via codebase operations.
