# Entity Resolution with Neo4j: Enron Email Dataset

An educational project demonstrating entity resolution techniques using Neo4j Graph Data Science on the Enron email corpus.

## What You'll Learn

- **Notebook 1**: Email parsing, graph modeling, and bulk data import into Neo4j
- **Notebook 2**: Advanced entity resolution using community detection, node similarity, and string matching algorithms

## Prerequisites

### Required Software
- **Python**: 3.9 or higher
- **Neo4j Desktop or Server**: 5.14+ with Graph Data Science (GDS) plugin installed
- **Memory**: Minimum 8GB RAM recommended (16GB preferred for full dataset)
- **Disk Space**: ~2GB for the dataset, ~5GB for Neo4j database

### Neo4j Setup
1. Install [Neo4j Desktop](https://neo4j.com/download/) or run Neo4j Server
2. Create a new database named `enrondemo`
3. Install the **Graph Data Science (GDS)** plugin (version 2.3+)
4. Start the database and note your connection details

## Quick Start

### 1. Clone and Setup Environment

```bash
git clone <your-repo-url>
cd Enron_Demo

# Create virtual environment
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt
```

### 2. Download the Dataset

Download the Enron email dataset:
```bash
cd Enron_Demo
wget https://www.cs.cmu.edu/~enron/enron_mail_20150507.tar.gz
tar -xzf enron_mail_20150507.tar.gz
```

This will create a `maildir/` folder with ~517,000 email files.

### 3. Configure Neo4j Connection

Copy the example environment file and add your credentials:

```bash
cp .env.example .env
```

Edit `.env` with your Neo4j details:
```
NEO4J_URI=neo4j://127.0.0.1:7687
NEO4J_USERNAME=neo4j
NEO4J_PASSWORD=your_password_here
```

### 4. Run the Notebooks

```bash
jupyter notebook demo_notebooks/
```

Start with `1_import_to_neo4j.ipynb`, then proceed to `2_entity_resolution.ipynb`.

## Dataset Information

The Enron email dataset contains emails from ~150 Enron employees, primarily senior management. The dataset was made public during the Federal Energy Regulatory Commission's investigation and has been re-redacted for privacy.

**Expected Import Results:**
- Emails: ~517,000
- User nodes: ~84,000
- Mailbox nodes: ~99,000
- Relationships: ~7.5 million

## Runtime Expectations

- **Notebook 1** (Import): 60-90 minutes for full dataset
- **Notebook 2** (Entity Resolution): 30-45 minutes

**Tip**: For quick testing, set `LIMIT = 10000` in Notebook 1, Cell 13 to process only the first 10,000 emails (~5 minutes).

## Graph Model

```
(User {nameRaw, nameNormalized, primaryEmail})
(Mailbox {address})
(Email {message_id, date, subject, thread})

(User)-[:OWNS]->(Mailbox)
(Mailbox)-[:SENT|RECEIVED|CC_ON|BCC_ON]->(Email)
(User)-[:SENT|RECEIVED|CC_ON|BCC_ON]->(Email)
```

After entity resolution:
```
(ResolvedEntity {canonical_name, email_addresses})
(User)-[:RESOLVES_TO]->(ResolvedEntity)
```

## Entity Resolution Approach

The project demonstrates a multi-stage entity resolution pipeline:

1. **Community Detection** (Louvain): Partition the graph into manageable communities
2. **Node Similarity**: Find users with overlapping mailbox connections
3. **String Matching** (Jaro-Winkler): Validate name similarity
4. **Connected Components** (WCC): Group all aliases together

This approach resolves ~5,000 duplicate entities with high confidence.

## Troubleshooting

**Neo4j connection fails:**
- Verify Neo4j is running: Check Neo4j Desktop or `systemctl status neo4j`
- Check credentials in `.env` match your Neo4j password
- Ensure database `enrondemo` exists

**Out of memory errors:**
- Reduce dataset size using `LIMIT` parameter
- Increase Neo4j heap size in `neo4j.conf`: `dbms.memory.heap.max_size=4G`
- Close other applications

**Import is slow:**
- This is normal! Processing 500k+ emails takes time
- Monitor progress with the tqdm progress bars
- Consider running overnight for full dataset

**GDS algorithms fail:**
- Verify GDS plugin is installed and activated
- Check GDS version: Should be 2.3+
- Restart Neo4j after installing GDS

## Project Structure

```
Enron_Demo/
├── demo_notebooks/
│   ├── 1_import_to_neo4j.ipynb      # Data import pipeline
│   └── 2_entity_resolution.ipynb    # Entity resolution workflow
├── requirements.txt                  # Python dependencies
├── .env.example                      # Environment template
├── .gitignore
└── README.md
```

## Further Learning

- [Neo4j Graph Data Science Documentation](https://neo4j.com/docs/graph-data-science/)
- [Entity Resolution Techniques](https://neo4j.com/developer/graph-data-science/entity-resolution/)
- [Enron Dataset Background](https://www.cs.cmu.edu/~enron/)

## License

This project is for educational purposes. The Enron email dataset is in the public domain.

## Acknowledgments

- Dataset: Carnegie Mellon University
- Techniques adapted from Neo4j Graph Data Science best practices
