# AI Conversation

**Conversational AI interface for Pathfinder 2E campaigns, powered by Forseti the Game Master using AWS Bedrock Claude 3.5 Sonnet with intelligent rolling summaries.**

## Badges

[![License: GPL-3.0](https://img.shields.io/badge/License-GPL%203.0-blue.svg)](https://www.gnu.org/licenses/gpl-3.0.html)
![Drupal Version](https://img.shields.io/badge/Drupal-9%20%7C%2010%20%7C%2011-blue)
![Status: Stable](https://img.shields.io/badge/Status-Stable-brightgreen)

## Overview

The AI Conversation module provides a sophisticated conversational AI interface for **Pathfinder 2E campaigns**, powered by **Forseti the Game Master** persona and **AWS Bedrock Claude 3.5 Sonnet**. It features an intelligent **rolling summary system** that allows for unlimited conversation length while maintaining narrative context and managing token costs. Each campaign conversation is stored as a persistent Drupal node, enabling multi-session campaigns, quest tracking, character interaction history, and full adventure audit trails. Perfect for collaborative storytelling, quest management, combat orchestration, and immersive roleplay experiences.

## Features

### 🎭 Forseti Game Master Persona
- **Immersive Narrative Voice**: All player-facing text uses D&D/Pathfinder terminology
- **Campaign Context Aware**: Maintains quest/campaign storyline across sessions
- **Character Integration**: Remember PC names, backgrounds, and relationships
- **World Building**: Rich descriptions of locations, NPCs, and encounters
- **Tone Control**: Adjust from humorous to serious, tactical to narrative-driven

### 🤖 AWS Bedrock Integration
- **Primary Model**: Claude 3.5 Sonnet (anthropic.claude-3-5-sonnet-20240620-v1:0)
- **Region**: us-west-2
- **Authentication**: Environment variables or IAM roles (no hardcoded credentials)
- **Fallback Models**: Claude 3 Haiku and Claude 3 Opus support
- **Error Handling**: Automatic retry with exponential backoff
- **Cost Tracking**: Per-message token accounting

### 🔄 Intelligent Rolling Summary System
- **Automatic Summarization**: Older messages summarized when conversation exceeds limits
- **Recent Message Retention**: Keeps the most recent N messages (default: 20) in full detail
- **Context Optimization**: Summary + recent messages provide optimal narrative flow
- **Configurable Frequency**: Summary updates every N messages (default: 10)
- **Token Management**: Prevents context window overflow in epic multi-session campaigns
- **Narrative Coherence**: Summaries preserve plot hooks and character development

### 💬 Real-time Chat Interface
- **AJAX-powered Messaging**: Instant responses without page refreshes
- **Live Statistics**: Real-time token count, message count, and session metrics
- **CSRF Protection**: Secure message sending with token validation
- **Access Control**: Players can only access their own campaign conversations
- **Progressive Enhancement**: Works with JavaScript disabled
- **Mobile Friendly**: Responsive design for tablets and phones

### 📊 Advanced Analytics & Monitoring
- **Campaign Statistics**: Session count, total playtime, quest progress
- **Token Tracking**: Comprehensive input/output token accounting
- **Combat Log**: Separate tracking for roll results and combat actions
- **Character Tracking**: NPC interactions and relationship changes
- **Debug Mode**: Detailed logging for troubleshooting

### 🎯 Node-Centric Architecture
- **Persistent Storage**: All campaign conversations stored as Drupal nodes
- **Custom Content Type**: `ai_conversation` with specialized D&D fields
- **Relationship Mapping**: Link to character sheets, quest logs, battle maps
- **Permission Control**: Drupal's native access control for players and GMs
- **Workflow Integration**: Compatible with campaign moderation workflows

## Installation

### Prerequisites
- Drupal 9, 10, or 11
- AWS Account with Bedrock access
- IAM credentials or instance role with Bedrock permissions
- Pathfinder 2E content modules (optional, for character integration)

### Installation Steps

```bash
# 1. Place module in custom modules directory
# Already located at: web/modules/custom/ai_conversation/

# 2. Enable the ai_conversation module
drush en ai_conversation -y

# 3. Install database schema
drush updatedb -y

# 4. Clear cache
drush cache:rebuild

# 5. Configure AWS Bedrock settings
drush config:set ai_conversation.settings bedrock_region us-west-2 -y

# 6. Set Forseti persona as default
drush config:set ai_conversation.settings default_persona forseti-gm -y
```

### Verify Installation

```bash
# Check module is enabled
drush pm:list --type=module --status=enabled | grep ai_conversation

# Verify chat interface route exists
curl -sI http://localhost/node/1/chat | head -2

# Test AWS Bedrock connectivity
drush php:eval "echo \Drupal::service('ai_conversation.bedrock_client')->testConnection();"

# Verify Forseti persona is configured
drush config:get ai_conversation.settings default_persona
```

## Configuration

### Module Settings

**Navigate to:** `admin/config/ai-conversation`

#### AWS Bedrock Settings
- **Region**: us-west-2 (configured for Bedrock availability)
- **Model ID**: anthropic.claude-3-5-sonnet-20240620-v1:0
- **Default Model**: Claude 3.5 Sonnet (recommended for narrative depth)
- **Enable Caching**: Cache context per model (recommended)

#### Forseti Game Master Configuration
- **GM Persona**: Default is "Forseti" (immersive Game Master voice)
- **Campaign Setting**: Pathfinder 2E (core ruleset)
- **Narrative Tone**: Select from:
  - Heroic (epic, dramatic)
  - Gritty (dark, serious)
  - Whimsical (humorous, light)
  - Balanced (mixed)
- **Lore Integration**: Load standard PF2E lore and setting details

#### Rolling Summary Configuration
- **Summary Trigger**: Number of messages before summarization (default: 10)
- **Recent Messages Count**: How many recent messages to retain (default: 20)
- **Max Context Tokens**: Token limit before forced summarization (default: 100,000)
- **Auto-Summarize**: Enable automatic summarization on message save
- **Preserve Plot Hooks**: Keep unresolved quests and cliffhangers in summaries

#### Campaign Settings
- **Session Duration Tracking**: Auto-mark session end after X minutes of inactivity
- **Character Limit**: Maximum number of PCs per campaign (default: 6)
- **Combat Mode**: Enable detailed combat tracking
- **Quest Tracking**: Auto-parse quest objectives and rewards
- **Loot Logging**: Track discovered treasure and magical items

#### Performance Settings
- **Max Response Time**: Timeout for API calls (default: 30 seconds)
- **Retry Attempts**: Number of retries on API failure (default: 3)
- **Token Cost Threshold**: Alert when conversation token count exceeds limit
- **Cache Lifetime**: TTL for cached model configurations (default: 1 day)

#### Debug & Monitoring
- **Debug Mode**: Enable detailed logging (disable in production)
- **Log API Calls**: Log all Bedrock requests/responses
- **Performance Logging**: Track response times and token usage
- **Error Notifications**: Alert GMs on API failures

### Permission Configuration

**Navigate to:** `admin/people/permissions`

Grant these permissions as needed:

| Permission | Role | Description |
|-----------|------|-------------|
| Administer AI Conversation | Admin | Full module configuration and debug access |
| Create AI Conversation | GM Role | Can create new campaign conversations |
| Edit Own AI Conversation | GM Role | Can edit their own campaign sessions |
| View AI Conversation | Player Role | Can view campaign they're in |
| Send Messages in AI Conversation | Player Role | Can send chat messages during session |
| Export Campaign Data | GM Role | Can export campaign logs and transcripts |
| Manage Characters | GM Role | Link PCs and NPCs to campaign |

### AWS Bedrock Configuration

Configure AWS credentials via environment variables:

```bash
# Option 1: Environment variables (development)
export AWS_ACCESS_KEY_ID=AKIAIOSFODNN7EXAMPLE
export AWS_SECRET_ACCESS_KEY=wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
export AWS_DEFAULT_REGION=us-west-2

# Option 2: IAM instance role (production - recommended)
# Attach IAM policy to EC2 instance with bedrock:InvokeModel permission
```

**Configuration File:** `settings.php` or `.env`

```php
// AWS Bedrock settings for DungeonCrawler
$config['ai_conversation.settings']['bedrock_region'] = 'us-west-2';
$config['ai_conversation.settings']['default_model'] = 'anthropic.claude-3-5-sonnet-20240620-v1:0';
$config['ai_conversation.settings']['enable_debug'] = FALSE;

// Forseti GM persona
$config['ai_conversation.settings']['default_persona'] = 'forseti-gm';
$config['ai_conversation.settings']['campaign_setting'] = 'pathfinder-2e';
$config['ai_conversation.settings']['narrative_tone'] = 'heroic';

// Summary settings
$config['ai_conversation.settings']['summary_trigger'] = 10;
$config['ai_conversation.settings']['recent_messages'] = 20;
$config['ai_conversation.settings']['preserve_plot_hooks'] = TRUE;
```

## Usage

### Step-by-Step Campaign Workflow

#### Step 1: Create Campaign Conversation Node

1. Navigate to **Content → Add content → AI Conversation**
2. Fill in campaign details:
   - **Title**: Campaign name (e.g., "The Godsmouth Heresy")
   - **AI Model**: Claude 3.5 Sonnet (default, recommended)
   - **Persona**: Forseti (Game Master)
   - **Context** (auto-populated): Campaign setting and rules
   
3. Optional configuration:
   - **Campaign Setting**: Absalom, Golarion, or custom
   - **Difficulty Level**: Moderate, Hard, Deadly, Insane
   - **Player Count**: Expected number of PCs
   - **Session Notes**: Campaign premise and objectives
   
4. Click **Save** to create your campaign node

#### Step 2: Start the Campaign

1. After saving, click **"Start Campaign"** or navigate to `/node/{nid}/chat`
   - Example: `https://dungeoncrawler.forseti.life/node/42/chat`
2. Campaign interface loads with:
   - Message history area (empty for new campaigns)
   - Message input field for player actions
   - Campaign statistics panel (XP, loot, session count)
   - Character roster showing party members
   - Quest log with active objectives

#### Step 3: Play Through Campaign Sessions

1. **PC Actions**: Players describe their characters' actions
   - Skill checks: "I want to climb the rope. I have +8 Athletics."
   - Combat: "I attack the goblin with my longsword."
   - Roleplay: "I try to persuade the tavern keeper to give us information."

2. **Forseti Responds**: Game Master uses narrative voice
   ```
   "As your blade arcs through the air, you feel it connect with 
    a satisfying thud. The goblin shrieks and stumbles backward, 
    blood seeping from the wound. Roll damage!"
   ```

3. **Outcomes**: All actions stored in campaign node with:
   - Timestamp of action
   - Player who took action
   - Result and consequences
   - Any rolls or mechanical effects

#### Step 4: Manage Campaign State

1. **Track Progress**:
   - Active quests with objectives
   - NPCs met and relationships
   - Locations discovered
   - Treasure acquired

2. **Session Management**:
   - Mark session complete
   - Summarize session events
   - Award XP and treasure
   - Plan next session

3. **Ongoing Refinement**:
   - Ask Forseti for clarifications
   - Request skill check descriptions
   - Adjust difficulty as needed
   - Track long-term plot developments

### Conversation Templates & Examples

#### Example 1: Introductory Quest Setup

```
GM: "Welcome to the city of Absalom! You find yourselves in the 
     bustling Silver Eel Tavern, the air thick with pipe smoke and 
     the clinking of mugs. A hooded figure in the corner waves you over..."

Player 1: "I approach the figure cautiously. I'm watching for any sign 
         of treachery. Perception check: 18."

GM: "As you weave through the crowd, you notice the figure's hand resting 
     on a concealed dagger. The tavern noise makes it hard to overhear 
     their conversation with a nearby sailor, but you catch fragments: 
     '...missing caravan...' and '...reward for recovery...'
     
     The figure looks up and gestures to a seat. 'Please, sit. I have 
     a proposition that might interest adventurers of your caliber.'"

Player 2: "I sit down and order an ale. What does this person look like?"

GM: "Drawing back their hood slightly, you see a middle-aged woman with 
     piercing gray eyes and a thin scar running down her left cheek. 
     She introduces herself as Harenna Vale, a merchant from Korvosa..."
```

#### Example 2: Combat Encounter

```
GM: "The dire wolf snarls, saliva dripping from its fangs. It lunges 
     at the rogue!"

[Forseti rolls and narrates combat round-by-round, tracking AC, HP, conditions]

Player 1: "I cast Fireball centered 20 feet above the wolves to hit all three!"

GM: "An excellent tactical choice! The wolves take the blast:
     Wolf 1: Failed Reflex save. Takes 18 damage (down to 7 HP).
     Wolf 2: Succeeded save. Takes 9 damage (down to 16 HP).
     Wolf 3: Failed save. Takes 19 damage (down to 6 HP).
     
     The smell of singed fur fills the air. Two wolves howl in pain 
     and back away, but the largest one snarls and presses forward..."
```

#### Example 3: Roleplay and NPC Interaction

```
Player: "I ask the innkeeper about recent disappearances in town."

GM: "The grizzled innkeeper leans on the bar, wiping a glass. 
     'Aye, there's been trouble, there has. Three merchants gone 
     missing in the last fortnight. All headed the same direction—
     the Old Mill Road, heading north. Ain't nothing good out that way.
     
     The city guard captain thinks it's bandits, but I got a feeling 
     it's something... worse.' He glances around nervously.
     
     Make an Occultism or Society check if you'd like to know more."

Player (rolls 16): "I got a 16 on Society."

GM: "Your knowledge of Absalom history surfaces: The Old Mill has been 
     abandoned for ten years. There were rumors of strange rituals 
     performed there by a cult before the city guard shut it down. 
     Three disappearances... all in two weeks... heading toward an 
     abandoned site with a dark history? This doesn't feel coincidental."
```

### Campaign Statistics & Tracking

The module tracks:

```
Campaign: The Godsmouth Heresy
├─ Sessions Completed: 12
├─ Total Playtime: 54 hours
├─ Party Level: 5
├─ XP Awarded: 18,500 total
├─ Loot Value: 8,200 gp
│
├─ Active Quests: 3
│  ├─ Primary: Rescue the Merchant Caravan
│  ├─ Secondary: Investigate the Cult
│  └─ Side: Help the Blacksmith's Daughter
│
├─ Party Roster: 4 members
│  ├─ Aldric (Human Fighter, Level 5)
│  ├─ Mira (Elf Rogue, Level 5)
│  ├─ Thorne (Dwarf Cleric, Level 5)
│  └─ Lyssa (Human Wizard, Level 5)
│
└─ Recent Events
   ├─ Session 12: Defeated the Cult Leader
   ├─ Session 11: Discovered the Cult's Lair
   ├─ Session 10: Tracked Caravan to Old Mill
   └─ Session 9: Met Harenna Vale at Tavern
```

### Advanced Features

#### Export Campaign

```bash
# Export full campaign as PDF
curl -H "Accept: application/pdf" \
  http://localhost/api/campaigns/42/export \
  > campaign_log.pdf

# Export as Markdown for editing
curl -H "Accept: text/markdown" \
  http://localhost/api/campaigns/42/export \
  > campaign_narrative.md

# Export combat log as spreadsheet
curl -H "Accept: text/csv" \
  http://localhost/api/campaigns/42/combat-log \
  > combat_log.csv
```

#### Programmatic Access

```php
// Load a campaign node
$campaign = Node::load(42);

// Access campaign details
$title = $campaign->label();
$pc_count = count(json_decode($campaign->field_party_roster->value));
$current_level = $campaign->field_party_level->value;

// Get recent campaign events
$messages = json_decode($campaign->field_messages->value, TRUE);
$recent = array_slice($messages, -10);

// Query campaign statistics
$stats = \Drupal::service('ai_conversation.campaign_stats')
  ->getCampaignStats(42);
echo "Total sessions: " . $stats->getSessionCount();
```

## Dependencies

### Required
- **Drupal Node Module**: Core entity storage system
- **Drupal Field Module**: Core field system for storing messages
- **Drupal User Module**: Authentication and user permissions
- **Drupal System Module**: Core system hooks and services

### Optional
- **Views Module**: For campaign listing and quest tracking
- **REST API Module**: For programmatic campaign access
- **Serialization Module**: For JSON/XML export functionality
- **PF2E Content Modules**: For character sheet integration

### External Services
- **AWS Bedrock**: Claude 3.5 Sonnet model inference
- **AWS Region**: us-west-2 (required for Bedrock availability)

### System Requirements
- PHP 8.0+ (8.2+ recommended)
- Composer (for AWS SDK)
- cURL (for HTTP requests)

## API Documentation

### REST Endpoints

#### Create Campaign

```
POST /api/campaigns
Content-Type: application/json

{
  "title": "The Godsmouth Heresy",
  "field_ai_model": "claude-3-5-sonnet",
  "field_campaign_setting": "absalom-golarion",
  "field_difficulty": "hard",
  "field_party_size": 4
}

Response: 201 Created
{
  "nid": 42,
  "uri": "/api/campaigns/42"
}
```

#### Send Player Action

```
POST /api/campaigns/42/action
Content-Type: application/json

{
  "action": "I cast Fireball at the goblin horde!",
  "player": "Lyssa (Wizard)",
  "check_type": "spell_attack",
  "check_result": "19"
}

Response: 200 OK
{
  "gm_response": "The spell erupts in a ball of flame...",
  "outcome": "success",
  "tokens_used": 342
}
```

#### Get Campaign Status

```
GET /api/campaigns/42

Response: 200 OK
{
  "title": "The Godsmouth Heresy",
  "party_level": 5,
  "sessions_completed": 12,
  "active_quests": 3,
  "party_roster": ["Aldric", "Mira", "Thorne", "Lyssa"],
  "last_session": 1704067200,
  "total_playtime_hours": 54
}
```

#### Export Campaign

```
GET /api/campaigns/42/export?format=pdf

Response: 200 OK
[PDF binary data]
```

### Drupal Hooks

#### Campaign Event Processing

```php
// Process campaign events
function my_module_ai_conversation_campaign_event(&$event, $campaign_node) {
  // Log combat events, track XP, etc.
  if ($event['type'] === 'combat_victory') {
    $xp = $event['xp_award'];
    \Drupal::logger('my_module')->info('Battle won: %xp XP awarded', [
      '%xp' => $xp
    ]);
  }
}
```

#### Character Tracking Hook

```php
// Track NPC relationships
function my_module_ai_conversation_npc_mention($npc_name, $action, $campaign_node) {
  // Track NPC interactions for relationship changes
  if ($action === 'hostility_increase') {
    \Drupal::logger('my_module')->notice('%npc is now hostile', [
      '%npc' => $npc_name
    ]);
  }
}
```

## Development

### Module Architecture

```
ai_conversation/ (DungeonCrawler variant)
├── src/
│   ├── Controller/
│   │   ├── CampaignController.php (Campaign management)
│   │   ├── ChatController.php (Chat interface and AJAX)
│   │   └── CombatController.php (Combat orchestration)
│   ├── Service/
│   │   ├── ChatService.php (Message processing)
│   │   ├── CampaignService.php (Campaign logic)
│   │   ├── CombatService.php (Combat mechanics)
│   │   ├── BedrockClient.php (AWS integration)
│   │   ├── SummaryService.php (Rolling summary)
│   │   └── TokenCounter.php (Token accounting)
│   ├── Plugin/
│   │   └── ... (Drupal integrations)
│   └── Persona/
│       └── ForsetiGMPersona.php (Game Master voice)
├── config/
│   ├── schema/
│   └── install/ (Default configuration)
├── templates/
│   ├── campaign-interface.html.twig
│   ├── character-roster.html.twig
│   └── quest-log.html.twig
├── js/
│   ├── campaign-chat.js (AJAX messaging)
│   ├── combat-tracker.js (Combat UI)
│   └── character-roster.js (PC management)
├── css/
│   └── campaign-interface.css (D&D styling)
└── ai_conversation.module (Hooks)
```

### Key Services

#### CampaignService

```php
// Create new campaign
$service = \Drupal::service('ai_conversation.campaign_service');
$node = $service->createCampaign('The Godsmouth Heresy', 'hard', 4);

// Get campaign statistics
$stats = $service->getCampaignStats($node);
echo "Sessions: " . $stats->getSessionCount();
```

#### CombatService

```php
// Process combat action
$combat_service = \Drupal::service('ai_conversation.combat_service');
$result = $combat_service->processAction(
  $campaign_node,
  'attack',
  ['target' => 'goblin', 'roll' => 18]
);
```

#### ForsetiGMPersona

```php
// Get GM response with Forseti voice
$persona = \Drupal::service('ai_conversation.forseti_persona');
$response = $persona->respondToAction($campaign_node, $player_action);
echo $response->getNarrative();  // Rich D&D description
```

### Testing

```bash
# Run unit tests
cd web/modules/custom/ai_conversation
../../../vendor/bin/phpunit tests/Unit/

# Run campaign simulation tests
../../../vendor/bin/phpunit tests/Functional/CampaignSimulation/

# Run with code coverage
../../../vendor/bin/phpunit --coverage-html=coverage/ tests/
```

### Local Development

```bash
# 1. Create test campaign
drush php:eval "
  \$node = \Drupal::entityTypeManager()
    ->getStorage('node')
    ->create([
      'type' => 'ai_conversation',
      'title' => 'Test Campaign',
      'field_campaign_setting' => 'absalom-golarion'
    ]);
  \$node->save();
  echo 'Campaign created: nid=' . \$node->id();
"

# 2. Enable debug mode
drush config:set ai_conversation.settings debug_mode TRUE -y

# 3. View debug logs
drush watchdog:tail ai_conversation

# 4. Access campaign at /node/{nid}/chat
```

### Performance Optimization

```php
// Cache campaign context
$cache = \Drupal::cache('default')->get('campaign_context_' . $nid);
if (!$cache) {
  $context = $this->buildCampaignContext($node);
  \Drupal::cache('default')->set('campaign_context_' . $nid, $context, 3600);
}

// Batch process session archives
$batch = [
  'title' => 'Archiving old campaigns...',
  'operations' => [
    ['ai_conversation_batch_archive_campaigns', []],
  ],
];
batch_set($batch);
```

## Contributing

### Contribution Guidelines

We welcome contributions! Please follow these guidelines:

1. **Fork & Branch**: Create a feature branch (`feature/my-feature`)
2. **Code Standards**: Follow Drupal coding standards (phpcs)
3. **Tests**: Add tests for new functionality
4. **Documentation**: Update this README for new features
5. **Commit Message**: Use descriptive messages with issue references

### Code Quality

```bash
# Check code standards
phpcs src/

# Fix formatting
phpcbf src/

# Run static analysis
phpstan --level=7 src/
```

### Reporting Issues

When reporting issues, please include:
- Drupal version
- Module version
- Campaign type and size
- Reproduction steps
- Expected vs. actual behavior
- Campaign statistics (message count, session count)

### Security Considerations

- **No Credentials in Code**: Never commit AWS keys or secrets
- **Input Validation**: Sanitize all player actions before API calls
- **Output Encoding**: Always escape GM responses in templates
- **CSRF Protection**: All forms include token validation
- **Access Control**: Verify player permissions before exposing campaign
- **Data Privacy**: Respect privacy for campaign and character data

### Performance Optimization

For epic multi-session campaigns:

```php
// Archive old sessions to improve performance
$archived = $this->archiveSessionsBefore($node, time() - (365 * 86400));
echo "Archived " . count($archived) . " sessions";

// Optimize message storage
\Drupal::database()->query('OPTIMIZE TABLE node__field_messages;');

// Token tracking optimization
\Drupal::cache('default')->set('campaign_tokens_' . $nid, $token_count, 3600);
```

## License

This module is licensed under the **GNU General Public License v3.0 (GPL-3.0-only)**.

See the LICENSE file for full details.

```
This program is free software: you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation, version 3 of the License.

This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the
GNU General Public License for more details.
```

## Support

### Getting Help

- **Documentation**: See ARCHITECTURE.md, DUNGEONCRAWLER_CONTEXT.md, and GENAI_CACHING.md
- **Issues**: File bugs via issue tracker
- **Community**: Ask questions in Drupal and D&D forums
- **Commercial Support**: Contact module maintainers

### Common Issues

#### Forseti Not Responding

```bash
# Check AWS Bedrock connectivity
drush php:eval "\Drupal::service('ai_conversation.bedrock_client')->testConnection();"

# Verify Forseti persona is configured
drush config:get ai_conversation.settings default_persona

# Check credentials
echo "Region: $AWS_DEFAULT_REGION"
echo "Access Key: ${AWS_ACCESS_KEY_ID:0:10}..."
```

#### Campaign Not Saving Messages

```bash
# Check field_messages database
drush sql:query "SELECT COUNT(*) FROM node__field_messages WHERE entity_id=42;"

# Verify permissions
drush php:eval "
  \$user = \Drupal\user\Entity\User::load(1);
  echo \$user->hasPermission('send messages in ai conversation') ? 'Yes' : 'No';
"

# Check for PHP errors
drush watchdog:tail ai_conversation
```

#### High Token Usage

```bash
# Monitor token spending per campaign
drush watchdog:tail ai_conversation | grep tokens

# Reduce recent message count for older campaigns
drush config:set ai_conversation.settings recent_messages 15 -y

# Archive old campaigns to reduce context size
drush eval "
  \$nodes = \Drupal::entityTypeManager()
    ->getStorage('node')
    ->loadByProperties(['type' => 'ai_conversation', 'created' => [time() - 15552000, '<']]);
"
```

#### Combat Not Tracking Properly

```bash
# Check combat log table
drush sql:query "SELECT * FROM copilot_agent_tracker_events WHERE type LIKE '%combat%' ORDER BY timestamp DESC LIMIT 10;"

# Verify combat service is loaded
drush php:eval "echo \Drupal::service('ai_conversation.combat_service') ? 'Loaded' : 'Not loaded';"
```

## Security

### Security Considerations

#### Authentication & Authorization
- **GM Only**: Campaign creation restricted to Game Masters
- **Player Visibility**: Players only see campaigns they're members of
- **CSRF Protection**: All AJAX endpoints use Drupal's token validation
- **API Authorization**: Requires user authentication for REST endpoints

#### Data Protection
- **AWS Encryption**: Data in transit over HTTPS to AWS Bedrock
- **Input Sanitization**: All player actions validated before API calls
- **Output Escaping**: All GM responses escaped in templates
- **No Credential Storage**: AWS credentials never stored in database

#### Audit Trail
- **Action Logging**: All player actions timestamped and stored
- **Campaign History**: Full audit trail of campaign progression
- **Admin Visibility**: Administrators can review any campaign
- **Compliance Ready**: Suitable for GDPR compliance

### Reporting Security Issues

Do not file public security issues. Instead:
1. Email security concerns to maintainers
2. Include detailed reproduction steps
3. Allow 90 days for response and patching

## Maintenance

### Upgrade Path

```bash
# Update module
cd web/modules/custom/ai_conversation
git pull origin main

# Run database updates
drush updatedb -y

# Clear cache
drush cache:rebuild

# Verify
drush pm:list --type=module | grep ai_conversation
```

### Database Maintenance

```bash
# Optimize message storage
drush sql:query "OPTIMIZE TABLE node__field_messages, node__field_conversation_summary;"

# Archive very old campaigns (2+ years)
drush sql:query "
  UPDATE node SET status=0 
  WHERE type='ai_conversation' AND created < DATE_SUB(NOW(), INTERVAL 2 YEAR)
"

# View database stats
drush sql:query "
  SELECT 
    'campaigns' as metric, COUNT(*) as count 
  FROM node 
  WHERE type='ai_conversation'
  UNION ALL
  SELECT 
    'total_messages', SUM(field_message_count_value)
  FROM node__field_message_count
  WHERE entity_id IN (SELECT nid FROM node WHERE type='ai_conversation');
"
```

### Monitoring & Performance

Monitor these metrics:

| Metric | Target | Action if Exceeded |
|--------|--------|-------------------|
| Average Response Time | < 3s | Optimize Forseti context |
| API Errors | < 0.5% | Check AWS Bedrock status |
| Token Usage per Campaign | < 500K | Archive or summarize |
| Database Size | < 50GB | Archive old campaigns |
| Failed Actions | < 1% | Review error logs |

### Version History

- **1.0.0** (Feb 2026): Initial release with Forseti persona and Bedrock integration
- **1.1.0** (Future): Full PF2E character sheet integration
- **1.2.0** (Future): Multi-table campaign management
- **2.0.0** (Future): Custom rulesets and world settings
