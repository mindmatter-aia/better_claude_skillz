# Stats Command

Display comprehensive statistics about your Claude Code session, context usage, and system configuration.

## Usage

`/stats [category]`

## Categories

- `context` - Context window and token usage
- `mcps` - Active MCP servers and tools
- `system` - Installed components (agents, skills, rules, commands)
- `session` - Current session information
- `all` or no argument - Show everything

## Implementation

When invoked:

1. **Context Window Stats**
   ```
   Read conversation metadata to estimate:
   - Messages in conversation
   - Approximate tokens used (messages * avg 500 tokens)
   - Estimated remaining capacity
   - Compact recommendations
   ```

2. **MCP Statistics**
   ```bash
   # Count configured MCPs
   grep -c '"command":' ~/.claude.json || echo "0"

   # List active MCPs
   grep -B 2 '"command":' ~/.claude.json | grep -E '^\s*"[^"]+":' | sed 's/.*"\([^"]*\)".*/\1/'

   # Estimate tool count (each MCP provides ~5-15 tools)
   ```

3. **System Components**
   ```bash
   # Count installed components
   echo "Agents: $(ls -1 ~/.claude/agents/*.md 2>/dev/null | wc -l)"
   echo "Skills: $(ls -1 ~/.claude/skills/ 2>/dev/null | wc -l)"
   echo "Rules: $(ls -1 ~/.claude/rules/*.md 2>/dev/null | wc -l)"
   echo "Commands: $(ls -1 ~/.claude/commands/*.md 2>/dev/null | wc -l)"
   echo "Hooks: $(grep -c '"hooks":' ~/.claude/settings.json 2>/dev/null || echo 0)"
   ```

4. **Session Information**
   ```
   - Current model (Sonnet/Opus/Haiku)
   - Working directory
   - Session start time (approximate from conversation)
   - Project configuration
   ```

## Output Format

```
=== CLAUDE CODE SESSION STATISTICS ===

CONTEXT WINDOW
--------------
Messages:           42 messages
Estimated tokens:   ~21,000 tokens used
Available:          ~179,000 tokens remaining (89%)
Status:             Healthy ✓
Recommendation:     No compaction needed

MCP SERVERS
-----------
Configured:         3 MCPs
Active:             memory, sequential-thinking, github
Estimated tools:    ~25-40 tools loaded
Context impact:     Low (3 MCPs < 6 recommended)
Status:             Optimal ✓

SYSTEM COMPONENTS
-----------------
Agents:             12 specialized subagents
Skills:             16 workflow patterns
Rules:              8 always-follow guidelines
Commands:           24 available commands
Hooks:              16 hooks configured
Hook scripts:       6 automation scripts

SESSION INFO
------------
Model:              Claude Sonnet 4.5
Working directory:  /home/user/projects/my-app
Session duration:   ~45 minutes
Project:            my-app

HEALTH CHECK
------------
✓ Context window healthy (89% available)
✓ MCP count optimal (3 active)
✓ All components loaded
✓ No issues detected

RECOMMENDATIONS
---------------
• Context usage is healthy
• Consider using /checkpoint before major changes
• Use /verify after implementation
```

## Context Estimation Logic

Since exact token counts aren't available via command, estimate using:

1. **Message Count**
   - Count conversation messages
   - Estimate ~300-700 tokens per message (depends on tool use)
   - Average: ~500 tokens per message

2. **MCP Overhead**
   - Each MCP adds ~5-15 tools to context
   - Tool definitions: ~100-300 tokens per tool
   - 3 MCPs ≈ ~3,000-4,500 tokens

3. **Base Context**
   - Rules: ~2,000 tokens
   - Skills metadata: ~1,000 tokens
   - System prompt: ~1,000 tokens

4. **Total Calculation**
   ```
   Used = (messages × 500) + (mcps × 1000) + 4000
   Available = 200000 - Used
   Percentage = (Available / 200000) × 100
   ```

5. **Status**
   - Healthy: > 70% available (> 140k tokens)
   - Warning: 30-70% available (60k-140k tokens)
   - Critical: < 30% available (< 60k tokens)

## Health Recommendations

Based on context usage:

- **> 70% available**: No action needed
- **50-70% available**: Consider checkpoint before major changes
- **30-50% available**: Recommend manual compaction after current task
- **< 30% available**: Suggest compaction or new session

## Examples

```
# Show all stats
/stats

# Show only context info
/stats context

# Show only MCP info
/stats mcps

# Show system components
/stats system

# Show session info
/stats session
```

## Notes

- Token estimates are approximations (exact counts not available via command)
- Context usage updates in real-time during conversation
- MCP tool counts are estimates (actual count varies by server)
- Use `/verify` to check actual build/test status
- Use `/checkpoint` to save state before major changes
