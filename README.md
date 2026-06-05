# 🏀 PAO BC Calendar Scraper

Αυτόματο scraping του προγράμματος του Panathinaikos BC από το [paobc.gr](https://www.paobc.gr/schedule/) και συγχρονισμός με Google Calendar.

## ✨ Features

- 🔄 Αυτόματος συγχρονισμός αγώνων με Google Calendar
- 📅 Υποστήριξη pagination (πολλές σελίδες)
- 🕒 Έλεγχος και ενημέρωση ωρών αγώνων
- 🗑️ Διαγραφή ακυρωμένων/μετακινημένων αγώνων
- ⏰ Υπενθύμιση 1 ώρα πριν τον αγώνα
- 🌍 Υποστήριξη timezone (Europe/Athens)
- 🔐 Service Account authentication (χωρίς token expiration!)

## 🚀 Εγκατάσταση

### Για χρήστες που θέλουν το δικό τους ημερολόγιο

**Σημαντικό**: Κάνοντας fork αυτό το repository, θα δημιουργήσεις το **δικό σου ξεχωριστό ημερολόγιο**. Δεν θα έχεις πρόσβαση στο δικό μου ημερολόγιο και δεν θα μπορείς να το τροποποιήσεις.

#### Βήματα:
1. Κάνε **Fork** αυτό το repository στο δικό σου GitHub account
2. Ακολούθησε τα παρακάτω βήματα για να στήσεις το δικό σου Google Calendar API
3. Πρόσθεσε τα **δικά σου** GitHub Secrets

### Προαπαιτούμενα

- Python 3.11+
- Google Cloud Project με Service Account

### 1. Clone το repository (ή το fork σου)
```bash
git clone https://github.com/YOUR_USERNAME/PaoBcScraper.git
cd PaoBcScraper
```

### 2. Εγκατάσταση dependencies
```bash
pip install requests beautifulsoup4
pip install google-auth google-auth-oauthlib google-auth-httplib2
pip install google-api-python-client
```

### 3. Google Service Account Setup

#### A. Δημιουργία Service Account

1. Πήγαινε στο [Google Cloud Console](https://console.cloud.google.com/)
2. Δημιούργησε νέο project (ή επίλεξε υπάρχον)
3. Πήγαινε στο **IAM & Admin** → **Service Accounts**
4. Πάτα **Create Service Account**
   - Name: `pao-scraper` (ή όποιο όνομα θέλεις)
   - Πάτα **Create and Continue**
   - **Παράλειψε** το Grant this service account access (πάτα Continue)
   - Πάτα **Done**

#### B. Δημιουργία JSON Key

1. Βρες το service account που μόλις δημιούργησες
2. Πάτα τα 3 τελείες (⋮) → **Manage Keys**
3. **Add Key** → **Create New Key** → **JSON**
4. Κατέβασε το JSON αρχείο
5. Μετονόμασέ το σε `service-account-key.json` και βάλτο στον root φάκελο

#### C. Ενεργοποίηση Google Calendar API

1. Στο Google Cloud Console → **APIs & Services** → **Library**
2. Ψάξε "Google Calendar API"
3. Πάτα **Enable**

#### D. Μοιράσου το Calendar με το Service Account

1. Άνοιξε το [Google Calendar](https://calendar.google.com)
2. Δημιούργησε νέο calendar (ή χρησιμοποίησε υπάρχον):
   - Αριστερά δίπλα στα "Other calendars" → **+** → **Create new calendar**
   - Όνομα: "PAO BC Calendar" (ή όποιο θέλεις)
3. Στο calendar που θέλεις να χρησιμοποιήσεις:
   - Πάτα τα 3 τελείες δίπλα του → **Settings and sharing**
   - Κύλησε κάτω στο **"Share with specific people"**
   - Πάτα **Add people**
   - Βάλε το email του service account (από το JSON, π.χ. `pao-scraper@project-id.iam.gserviceaccount.com`)
   - Permission: **Make changes to events**
   - Πάτα **Send**

### 4. Ρύθμιση Calendar ID (προαιρετικό)

Αν θες να χρησιμοποιήσεις συγκεκριμένο calendar:
```bash
# Δημιούργησε το .env αρχείο
cp .env.example .env

# Επεξεργάσου το .env και βάλε το δικό σου Calendar ID
CALENDAR_ID=your_calendar_id@group.calendar.google.com
```

Αν δεν ορίσεις `CALENDAR_ID`, θα χρησιμοποιηθεί το **primary** calendar σου.

## 💻 Χρήση

### Τοπική εκτέλεση
```bash
python pao_scraper.py
```

**Δεν χρειάζεται browser authentication!** Το service account χρησιμοποιεί το JSON key file.

### Αυτόματη εκτέλεση με GitHub Actions

Το scraper τρέχει αυτόματα **κάθε μέρα στις 10:00 πρωί** (ώρα Ελλάδας / 08:00 UTC).

#### Ρύθμιση GitHub Secrets:

1. Στο **fork σου**, πήγαινε στο: `Settings` → `Secrets and variables` → `Actions`
2. Πρόσθεσε το **δικό σου** secret:
   - Name: `SERVICE_ACCOUNT_KEY`
   - Value: Το **base64-encoded** περιεχόμενο του `service-account-key.json`

#### Πώς να κάνω encode το service-account-key.json σε base64:

**Windows (PowerShell):**
```powershell
[Convert]::ToBase64String([IO.File]::ReadAllBytes("service-account-key.json")) | clip
```

**Windows (Git Bash):**
```bash
cat service-account-key.json | base64 -w 0
```

**Linux/Mac:**
```bash
cat service-account-key.json | base64 -w 0
```

Αντίγραψε το output και βάλτο ως το `SERVICE_ACCOUNT_KEY` secret.

#### Manual Trigger

Μπορείς επίσης να τρέξεις το workflow χειροκίνητα:
1. Πήγαινε στο tab **Actions**
2. Επίλεξε **"PAO BC Scraper (Service Account)"**
3. Πάτα **"Run workflow"**

## 📁 Δομή Αρχείων
```
PaoBcScraper/
├── pao_scraper.py              # Main script
├── clean_calendar.py           # Utility για καθαρισμό calendar
├── service-account-key.json    # Service Account credentials (local only)
├── .github/
│   └── workflows/
│       └── scraper.yml         # GitHub Actions workflow
├── .gitignore                  # Ignored files
├── .env.example                # Template για environment variables
└── README.md
```

## 🔒 Ασφάλεια

Τα παρακάτω αρχεία **ΔΕΝ** ανεβαίνουν στο GitHub (προστατεύονται από .gitignore):
- `service-account-key.json` - Service Account credentials
- `credentials.json` - OAuth credentials (παλιό, δεν χρειάζεται πια)
- `token.json` / `token.pickle` - Access tokens (παλιό, δεν χρειάζεται πια)
- `.env` - Local configuration

**Προσοχή**: Μην κάνεις ποτέ commit το `service-account-key.json`! Περιέχει ευαίσθητα δεδομένα.

## 🆚 Service Account vs OAuth

Το project χρησιμοποιεί **Service Account** αντί για OAuth γιατί:

✅ **Service Account (Current):**
- Δεν λήγει ποτέ το authentication
- Ιδανικό για automation/CI/CD
- Δεν χρειάζεται browser authentication
- Πιο αξιόπιστο για scheduled tasks

❌ **OAuth (Deprecated):**
- Tokens λήγουν κάθε 7 μέρες (testing mode)
- Χρειάζεται manual reauthorization
- Προβλήματα με GitHub Actions

## 🛠️ Troubleshooting

### "Δεν βρέθηκαν service account credentials"
- Βεβαιώσου ότι το `service-account-key.json` υπάρχει στον root φάκελο (local)
- Ή ότι έχεις ορίσει το `SERVICE_ACCOUNT_KEY` secret (GitHub Actions)

### "403 Forbidden" ή "Insufficient Permission"
- Βεβαιώσου ότι έχεις μοιραστεί το calendar με το service account email
- Έλεγξε ότι το permission είναι "Make changes to events"

### "Calendar not found"
- Έλεγξε ότι το `CALENDAR_ID` είναι σωστό
- Ή χρησιμοποίησε `primary` για το default calendar

### GitHub Actions αποτυγχάνει
- Έλεγξε ότι το `SERVICE_ACCOUNT_KEY` secret είναι σωστά encoded σε base64
- Δες τα logs στο Actions tab για λεπτομερή σφάλματα

## 📊 Πώς δουλεύει

1. **Σάρωση**: Το script σαρώνει όλες τις σελίδες του paobc.gr/schedule
2. **Parsing**: Εξάγει πληροφορίες αγώνων (ομάδες, ημερομηνία, ώρα, γήπεδο, διοργάνωση)
3. **Σύγκριση**: Συγκρίνει με τα υπάρχοντα events στο Google Calendar
4. **Συγχρονισμός**:
   - Προσθέτει νέους αγώνες
   - Ενημερώνει αγώνες που άλλαξαν ώρα
   - Διαγράφει αγώνες που δεν υπάρχουν πια (ακυρώθηκαν/μετακινήθηκαν)

## 📝 License

MIT License - Ελεύθερο για προσωπική χρήση

## 🤝 Contributing

Pull requests are welcome! Για μεγάλες αλλαγές, άνοιξε πρώτα ένα issue.

## ⚠️ Disclaimer

Αυτό το project είναι ανεπίσημο και δεν έχει καμία σχέση με την ΚΑΕ Παναθηναϊκός. Χρησιμοποιεί δημόσια διαθέσιμες πληροφορίες από το επίσημο website.

## 🙏 Credits

- Developed by [@RhamnusiaCodes](https://github.com/RhamnusiaCodes)
- Data source: [paobc.gr](https://www.paobc.gr)

---

**Φτιαγμένο με 💚 για τον Παναθηναϊκό!**
