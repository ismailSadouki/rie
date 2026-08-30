
![](https://i.imgur.com/6Yicdej.png)


You are a research assistant filling a structured personal database for one specific student. You are not chatting.

STUDENT
- Master 2 (final year), Statistics and Data Science, ENSSEA, Algeria.
- Graduates June/September 2027. PFE window: 1 Feb 2027 → 31 Jul 2027.
- Prior teaching in French; English is the working academic language; IELTS not yet taken.
- Core: statistics, data science, ML, deep learning, NLP. Engineering: Python, PyTorch, Hugging Face, LoRA/QLoRA, DPO/GRPO, Docker, FastAPI, Linux, GPU/CUDA.
- Deepening: LLM systems, inference, GPU memory, CUDA, serving, inference optimisation.
- Exposure: Darija NLP, preference optimisation, LLM evaluation, LLM inference systems, NTN, ray tracing, channel modelling.
- Targets: PFE + research internships; Master's abroad Sept 2027 (Germany, France, Canada/Québec, Italy); backup Switzerland Sept 2028; eventual PhD.
- Not optimising for certificates.

CURRENT DATABASE (avoid duplicates; reuse these ids when referring to existing records):
{
 "profile": {
  "status": "Master 2 (final year), Statistics and Data Science, ENSSEA, Algeria",
  "graduation": "June/September 2027",
  "pfeWindow": "2027-02-01 to 2027-07-31",
  "languages": "Prior teaching in French; English working language; IELTS not yet taken",
  "ieltsStatus": "Missing",
  "goals": [
   "PFE / research internship from Feb 2027",
   "Master's abroad, September 2027 intake (Germany, France, Canada/Québec, Italy)",
   "Backup: Switzerland, September 2028",
   "Long term: research-oriented ML/AI, possible PhD"
  ],
  "targetProfile": "Statistics & Data Science + ML + NLP/LLMs + LLM inference systems"
 },
 "existingOpportunities": [
  {
   "id": "opp_inria_pfe",
   "title": "PFE / M2 research internship — Inria Paris (ALMAnaCH): multilingual & low-resource NLP",
   "org": "Inria Paris — ALMAnaCH team",
   "type": "PFE",
   "country": "France",
   "status": "Shortlisted",
   "fit": 96
  },
  {
   "id": "opp_mila_intern",
   "title": "Research internship — Mila (MyMila portal, supervisor-funded)",
   "org": "Mila — Quebec AI Institute (Université de Montréal / McGill)",
   "type": "Research Internship",
   "country": "Canada",
   "status": "Discovered",
   "fit": 90
  },
  {
   "id": "opp_saarland_pfe",
   "title": "PFE / research internship — Spoken Language Systems (LSV), Saarland University",
   "org": "Saarland University (LSV) — ELLIS Saarbrücken / MPI-INF ecosystem",
   "type": "PFE",
   "country": "Germany",
   "status": "Discovered",
   "fit": 93
  },
  {
   "id": "opp_naver_pfe",
   "title": "PFE / research internship — multilingual NLP (Naver Labs Europe)",
   "org": "Naver Labs Europe",
   "type": "PFE",
   "country": "France",
   "status": "Discovered",
   "fit": 88
  },
  {
   "id": "opp_tum_daml",
   "title": "Research internship — Data Analytics and Machine Learning group (TUM)",
   "org": "Technical University of Munich — DAML",
   "type": "Research Internship",
   "country": "Germany",
   "status": "Discovered",
   "fit": 84
  },
  {
   "id": "opp_sapienza",
   "title": "Research internship / thesis — Sapienza NLP Group (multilingual NLP)",
   "org": "Sapienza University of Rome",
   "type": "Research Internship",
   "country": "Italy",
   "status": "Discovered",
   "fit": 86
  },
  {
   "id": "opp_pfe_enssea",
   "title": "PFE fallback — ENSSEA (Darija preference optimisation, with external co-supervision)",
   "org": "ENSSEA — École Nationale Supérieure de Statistique et d'Économie Appliquée",
   "type": "PFE",
   "country": "Algeria",
   "status": "Preparing",
   "fit": 92
  },
  {
   "id": "opp_hf_intern",
   "title": "Internship — ML / LLM ecosystem (Hugging Face)",
   "org": "Hugging Face",
   "type": "Internship",
   "country": "France",
   "status": "Discovered",
   "fit": 85
  },
  {
   "id": "opp_mistral_intern",
   "title": "Internship — ML Engineering / LLM (Mistral AI, Paris)",
   "org": "Mistral AI",
   "type": "Internship",
   "country": "France",
   "status": "Discovered",
   "fit": 82
  },
  {
   "id": "opp_daad_schol",
   "title": "DAAD Study Scholarships — Master Studies for All Academic Disciplines",
   "org": "DAAD (German Academic Exchange Service)",
   "type": "Scholarship",
   "country": "Germany",
   "status": "Researching",
   "fit": 92
  },
  {
   "id": "opp_profas",
   "title": "PROFAS+ — Algerian national programme for training abroad",
   "org": "Ministry of Higher Education and Scientific Research (Algeria)",
   "type": "Scholarship",
   "country": "Algeria",
   "status": "Researching",
   "fit": 80
  },
  {
   "id": "opp_eiffel",
   "title": "Eiffel Excellence Scholarship — Master's component",
   "org": "Campus France / Ministry for Europe and Foreign Affairs",
   "type": "Scholarship",
   "country": "France",
   "status": "Discovered",
   "fit": 76
  },
  {
   "id": "opp_erasmus_lct",
   "title": "Erasmus Mundus Joint Master — Language and Communication Technologies (LCT)",
   "org": "LCT consortium (Erasmus Mundus)",
   "type": "Master",
   "country": "Multiple EU",
   "status": "Researching",
   "fit": 94
  },
  {
   "id": "opp_job_eu_llm",
   "title": "Junior ML / LLM engineer — European AI companies (graduate 2027)",
   "org": "Mistral AI · Hugging Face · Poolside · Black Forest Labs · Scaleway · LightOn",
   "type": "Job",
   "country": "France",
   "status": "Discovered",
   "fit": 80
  },
  {
   "id": "opp_phd_watch",
   "title": "PhD positions — LLM systems / NLP (long-term watch)",
   "org": "European labs (EPFL, ETH, Mila, Inria)",
   "type": "PhD",
   "country": "Europe",
   "status": "Discovered",
   "fit": 72
  },
  {
   "id": "opp_example",
   "title": "Research internship — efficient Transformer inference",
   "org": "Example Lab",
   "type": "Research Internship",
   "country": "Germany",
   "status": "Discovered",
   "fit": 88
  }
 ],
 "existingMasters": [
  {
   "id": "mas_tum_dea",
   "university": "Technical University of Munich (TUM)",
   "program": "MSc Data Engineering and Analytics",
   "intake": "September 2027",
   "status": "Researching",
   "tier": "Realistic"
  },
  {
   "id": "mas_tum_mds",
   "university": "Technical University of Munich (TUM)",
   "program": "MSc Mathematics in Data Science",
   "intake": "September 2027",
   "status": "Researching",
   "tier": "Realistic"
  },
  {
   "id": "mas_lmu_sds",
   "university": "LMU Munich",
   "program": "MSc Statistics and Data Science",
   "intake": "September 2027",
   "status": "Researching",
   "tier": "Realistic"
  },
  {
   "id": "mas_ups_mva",
   "university": "ENS Paris-Saclay / Université Paris-Saclay",
   "program": "M2 MVA — Mathematics, Vision, Learning",
   "intake": "September 2027",
   "status": "Researching",
   "tier": "Ambitious"
  },
  {
   "id": "mas_ups_ai",
   "university": "Université Paris-Saclay (LISN)",
   "program": "M2 Artificial Intelligence / AI Master Track",
   "intake": "September 2027",
   "status": "Researching",
   "tier": "Ambitious"
  },
  {
   "id": "mas_udem_mila",
   "university": "Université de Montréal (Mila)",
   "program": "MSc in Computer Science — research stream",
   "intake": "September 2027",
   "status": "Researching",
   "tier": "Ambitious"
  },
  {
   "id": "mas_polimi",
   "university": "Politecnico di Milano",
   "program": "MSc Computer Science and Engineering",
   "intake": "September 2027",
   "status": "Preparing",
   "tier": "Safer"
  },
  {
   "id": "mas_saarland",
   "university": "Saarland University",
   "program": "MSc Data Science and Artificial Intelligence",
   "intake": "September 2027",
   "status": "Discovered",
   "tier": "Realistic"
  },
  {
   "id": "mas_eth",
   "university": "ETH Zürich",
   "program": "MSc Data Science",
   "intake": "September 2028",
   "status": "Discovered",
   "tier": "Ambitious"
  },
  {
   "id": "mas_epfl",
   "university": "EPFL",
   "program": "MSc in Data Science / Computer Science",
   "intake": "September 2028",
   "status": "Discovered",
   "tier": "Ambitious"
  },
  {
   "id": "mas_mcgill",
   "university": "McGill University",
   "program": "MSc in Computer Science (thesis)",
   "intake": "September 2028",
   "status": "Discovered",
   "tier": "Safer"
  }
 ],
 "existingResearchers": [
  {
   "id": "res_sagot",
   "name": "Benoît Sagot",
   "university": "Inria Paris",
   "area": "Multilingual NLP, low-resource and historical languages",
   "status": "Good Fit",
   "contacted": false
  },
  {
   "id": "res_besacier",
   "name": "Laurent Besacier",
   "university": "Naver Labs Europe",
   "area": "Multilingual NLP, low-resource machine translation, speech translation",
   "status": "Discovered",
   "contacted": false
  },
  {
   "id": "res_klakow",
   "name": "Dietrich Klakow",
   "university": "Saarland University",
   "area": "Spoken language systems, LLM uncertainty, retrieval",
   "status": "Discovered",
   "contacted": false
  },
  {
   "id": "res_guennemann",
   "name": "Stephan Günnemann",
   "university": "Technical University of Munich",
   "area": "Deep learning, robustness, graph ML",
   "status": "Discovered",
   "contacted": false
  },
  {
   "id": "res_bischl",
   "name": "Bernd Bischl",
   "university": "LMU Munich",
   "area": "Statistical learning and data science, AutoML",
   "status": "Discovered",
   "contacted": false
  },
  {
   "id": "res_navigli",
   "name": "Roberto Navigli",
   "university": "Sapienza University of Rome",
   "area": "Multilingual NLP, word sense disambiguation, knowledge from text",
   "status": "Discovered",
   "contacted": false
  },
  {
   "id": "res_reddy",
   "name": "Siva Reddy",
   "university": "McGill University / Mila",
   "area": "NLP, multilingual and grounded language understanding",
   "status": "Discovered",
   "contacted": false
  },
  {
   "id": "res_mitliagkas",
   "name": "Ioannis Mitliagkas",
   "university": "Université de Montréal / Mila",
   "area": "Optimization for machine learning",
   "status": "Discovered",
   "contacted": false
  },
  {
   "id": "res_allauzen",
   "name": "Alexandre Allauzen",
   "university": "Université Paris-Saclay (LISN / CNRS)",
   "area": "NLP, multilingual models, efficient neural networks",
   "status": "Discovered",
   "contacted": false
  },
  {
   "id": "res_moulines",
   "name": "Éric Moulines",
   "university": "École Polytechnique (CMAP)",
   "area": "Statistics, Monte Carlo methods, machine learning theory",
   "status": "Discovered",
   "contacted": false
  },
  {
   "id": "res_bosselut",
   "name": "Antoine Bosselut",
   "university": "EPFL",
   "area": "Natural language processing, large language models",
   "status": "Discovered",
   "contacted": false
  },
  {
   "id": "res_jaggi",
   "name": "Martin Jaggi",
   "university": "EPFL",
   "area": "Machine learning optimization, efficient and federated training",
   "status": "Discovered",
   "contacted": false
  },
  {
   "id": "res_hoefler",
   "name": "Torsten Hoefler",
   "university": "ETH Zürich",
   "area": "High-performance computing and ML systems",
   "status": "Discovered",
   "contacted": false
  }
 ],
 "existingLabs": [
  {
   "id": "lab_almanach",
   "name": "ALMAnaCH — Automatic Language Modelling and Analysis & Computational Humanities",
   "university": "Inria Paris"
  },
  {
   "id": "lab_daml",
   "name": "DAML — Data Analytics and Machine Learning",
   "university": "Technical University of Munich"
  },
  {
   "id": "lab_lsv",
   "name": "LSV — Spoken Language Systems",
   "university": "Saarland University"
  },
  {
   "id": "lab_slds",
   "name": "SLDS — Statistical Learning and Data Science",
   "university": "LMU Munich"
  },
  {
   "id": "lab_mila",
   "name": "Mila — Quebec AI Institute",
   "university": "Université de Montréal / McGill (and partners)"
  },
  {
   "id": "lab_lisn",
   "name": "LISN — Laboratoire Interdisciplinaire des Sciences du Numérique (CNRS / Paris-Saclay)",
   "university": "Université Paris-Saclay"
  },
  {
   "id": "lab_naver",
   "name": "Naver Labs Europe",
   "university": "—"
  },
  {
   "id": "lab_sapienzanlp",
   "name": "Sapienza NLP Group",
   "university": "Sapienza University of Rome"
  },
  {
   "id": "lab_cmap",
   "name": "CMAP — Centre de Mathématiques Appliquées",
   "university": "École Polytechnique"
  },
  {
   "id": "lab_spcl",
   "name": "SPCL — Scalable Parallel Computing Laboratory",
   "university": "ETH Zürich"
  },
  {
   "id": "lab_mlo",
   "name": "MLO — Machine Learning and Optimization Laboratory",
   "university": "EPFL"
  },
  {
   "id": "lab_claire",
   "name": "CLAIRE — Computational Linguistics and Information Retrieval lab",
   "university": "EPFL"
  }
 ],
 "existingPapers": [
  {
   "id": "pap_paged",
   "title": "Efficient Memory Management for Large Language Model Serving with PagedAttention",
   "status": "Notes Done"
  },
  {
   "id": "pap_flashattention",
   "title": "FlashAttention: Fast and Memory-Efficient Exact Attention with IO-Awareness",
   "status": "Reading"
  },
  {
   "id": "pap_fa3",
   "title": "FlashAttention-3: Fast and Accurate Attention with Asynchrony and Low-precision",
   "status": "To Read"
  },
  {
   "id": "pap_gqa",
   "title": "GQA: Training Generalized Multi-Query Transformer Models from Multi-Head Checkpoints",
   "status": "To Read"
  },
  {
   "id": "pap_awq",
   "title": "AWQ: Activation-aware Weight Quantization for LLM Compression and Acceleration",
   "status": "To Read"
  },
  {
   "id": "pap_gptq",
   "title": "GPTQ: Accurate Post-Training Quantization for Generative Pre-trained Transformers",
   "status": "To Read"
  },
  {
   "id": "pap_dpo",
   "title": "Direct Preference Optimization: Your Language Model is Secretly a Reward Model",
   "status": "Reference"
  },
  {
   "id": "pap_grpo",
   "title": "DeepSeekMath: Pushing the Limits of Mathematical Reasoning in Open Language Models",
   "status": "To Read"
  },
  {
   "id": "pap_lora",
   "title": "LoRA: Low-Rank Adaptation of Large Language Models",
   "status": "Reference"
  },
  {
   "id": "pap_qlora",
   "title": "QLoRA: Efficient Finetuning of Quantized LLMs",
   "status": "Notes Done"
  },
  {
   "id": "pap_aya",
   "title": "Aya Model: An Instruction Finetuned Open-Access Multilingual Language Model",
   "status": "To Read"
  },
  {
   "id": "pap_are_dialects_better",
   "title": "Are Dialects Better Prompters? A Case Study on Arabic",
   "status": "To Read"
  },
  {
   "id": "pap_dziribert",
   "title": "DziriBERT: a Pre-Trained Language Model for Algerian Dialect / Goud.ma (Moroccan Darija summarisation)",
   "status": "To Read"
  },
  {
   "id": "pap_dialectbench",
   "title": "DIALECTBENCH: A NLP Benchmark for Languages, Dialects and Varieties",
   "status": "To Read"
  },
  {
   "id": "pap_oscar",
   "title": "Asynchronous Pipeline for Processing Huge Corpora on Medium to Low Resource Infrastructures (OSCAR)",
   "status": "To Read"
  },
  {
   "id": "pap_semunc",
   "title": "Semantic Uncertainty: Linguistic Invariances for Uncertainty Estimation in Natural Language Generation",
   "status": "To Read"
  }
 ],
 "existingProjects": [
  {
   "id": "prj_inference",
   "name": "Mini Inference Engine",
   "status": "Active"
  },
  {
   "id": "prj_dziridpo",
   "name": "DziriDPO",
   "status": "Active"
  },
  {
   "id": "prj_cs336",
   "name": "CS336 — language modelling from scratch",
   "status": "Shipped"
  },
  {
   "id": "prj_ntn",
   "name": "NTN Simulator",
   "status": "Paused"
  }
 ],
 "existingGaps": [
  {
   "id": "gap_kernels",
   "title": "Writing and profiling real GPU kernels",
   "severity": "Blocking"
  },
  {
   "id": "gap_quant",
   "title": "Quantisation methods (GPTQ / AWQ / INT4 serving)",
   "severity": "Blocking"
  },
  {
   "id": "gap_research_writing",
   "title": "Writing results like a researcher, not like a student",
   "severity": "Important"
  },
  {
   "id": "gap_eval_design",
   "title": "Designing a defensible evaluation for a dialect with no benchmark",
   "severity": "Blocking"
  },
  {
   "id": "gap_distributed",
   "title": "Distributed training (DDP / FSDP / sharding)",
   "severity": "Nice to have"
  },
  {
   "id": "gap_bayesian",
   "title": "Bayesian methods and uncertainty quantification",
   "severity": "Important"
  },
  {
   "id": "gap_english",
   "title": "IELTS Academic — band 6.5+ (7.0 for ETH/EPFL)",
   "severity": "Blocking"
  },
  {
   "id": "gap_french_admin",
   "title": "French academic administration vocabulary (EEF / CVEC / préfecture)",
   "severity": "Important"
  }
 ],
 "upcomingDeadlines": [
  {
   "title": "Book IELTS (target: sit the test in October)",
   "date": "2026-09-05",
   "category": "IELTS"
  },
  {
   "title": "Polimi Early Bird window opens (merit scholarship)",
   "date": "2026-10-01",
   "category": "Master deadline"
  },
  {
   "title": "Mila Supervision Request window opens",
   "date": "2026-10-15",
   "category": "Application deadline"
  },
  {
   "title": "DAAD Study Scholarships — Master's deadline",
   "date": "2026-11-16",
   "category": "Scholarship deadline"
  },
  {
   "title": "Polimi Early Bird window closes",
   "date": "2026-12-01",
   "category": "Master deadline"
  },
  {
   "title": "Mila Supervision Request window closes",
   "date": "2026-12-01",
   "category": "Application deadline"
  },
  {
   "title": "Campus France Études en France — open the file",
   "date": "2026-12-15",
   "category": "Other"
  },
  {
   "title": "Eiffel Excellence Scholarship — institution nomination deadline",
   "date": "2027-01-08",
   "category": "Scholarship deadline"
  },
  {
   "title": "UdeM Fall application deadline",
   "date": "2027-02-01",
   "category": "Master deadline"
  },
  {
   "title": "PFE start (expected)",
   "date": "2027-02-01",
   "category": "Other"
  },
  {
   "title": "Polimi English certificate deadline",
   "date": "2027-02-21",
   "category": "IELTS"
  },
  {
   "title": "Polimi international application deadline (Sept 2027 intake)",
   "date": "2027-02-26",
   "category": "Master deadline"
  },
  {
   "title": "Paris-Saclay AI Master M2 window (expected March – early April)",
   "date": "2027-03-15",
   "category": "Master deadline"
  },
  {
   "title": "LMU application deadline (expected ~15 May)",
   "date": "2027-05-15",
   "category": "Master deadline"
  },
  {
   "title": "TUM application window closes (1 Feb – 31 May)",
   "date": "2027-05-31",
   "category": "Master deadline"
  },
  {
   "title": "ENS Paris-Saclay MVA M2 window (expected 1 May – 30 June)",
   "date": "2027-06-30",
   "category": "Master deadline"
  },
  {
   "title": "Master's graduation (expected June 2027)",
   "date": "2027-06-30",
   "category": "Other"
  },
  {
   "title": "Saarland winter application deadline (expected ~15 July)",
   "date": "2027-07-15",
   "category": "Master deadline"
  }
 ]
}

TASK
Find REAL, CURRENT opportunities and records that match this profile. Prefer official pages.
1. PFE / M2 research internships starting ~Feb 2027, 4-6 months, open to a non-EU (Algerian) student, in: efficient LLM inference/serving, multilingual & low-resource NLP, LLM evaluation, LLM alignment (DPO/GRPO).
2. Research internships and research-assistant positions in 2027 in the same areas.
3. Master's programmes, September 2027 intake (Germany, France, Canada/Québec, Italy) with strong ML/NLP/LLM-systems content — with deadlines, tuition, English requirements and funding.
4. Scholarships open to Algerian students for a 2027 intake.
5. Researchers and labs in those areas, and 1-2 recent papers per researcher worth reading.
6. Any hard administrative deadline attached to the above (document verification, funding calls, visa routes).
Return up to 12 records total. Quality over quantity — 8 verified records beat 30 vague ones.

OUTPUT RULES
- Return ONLY a single JSON object. No prose, no markdown fences, no commentary before or after.
- Use only these collection keys: opportunities, masters, researchers, labs, papers, tasks, documents, contacts, events, evidence, visa, budgetItems, budgetSources, interviewTopics, projects, gaps
- Include ONLY collections you actually have data for. Partial output is welcome and expected.
- Never nest collections inside other keys. Top-level keys only.

ALLOWED ENUM VALUES (copy them EXACTLY, including capitalisation and punctuation):
  opportunityType: "PFE" | "Internship" | "Research Internship" | "Research Assistant" | "Master" | "PhD" | "Research Position" | "Job" | "Scholarship" | "Fellowship" | "Other"
  opportunityStatus: "Discovered" | "Researching" | "Shortlisted" | "Preparing" | "Ready to Apply" | "Contacted" | "Applying" | "Applied" | "Interview" | "Waiting" | "Follow-up Needed" | "Accepted" | "Rejected" | "Withdrawn" | "Archived"
  masterStatus: "Discovered" | "Researching" | "Shortlisted" | "Preparing" | "Ready" | "Applied" | "Interview" | "Waiting" | "Accepted" | "Rejected" | "Archived"
  masterIntake: "September 2027" | "September 2028"
  researcherStatus: "Discovered" | "Researching" | "Good Fit" | "Contacted" | "Follow-up" | "Response" | "Collaboration"
  researcherFunding: "Yes" | "Likely" | "Unknown" | "No"
  researcherResponse: "None" | "Positive" | "Neutral" | "Negative"
  paperCategory: "Relevant to opportunity" | "Relevant to researcher" | "Background" | "Implementation reference" | "Research direction" | "Future" | "Optional"
  paperStatus: "To Read" | "Reading" | "Notes Done" | "Reference"
  projectStatus: "Idea" | "Active" | "Paused" | "Shipped" | "Archived"
  taskStatus: "Todo" | "In Progress" | "Done"
  taskCategory: "Application" | "PFE" | "Internship" | "Research" | "Master" | "PhD" | "Job" | "Scholarship" | "Email" | "Administrative"
  docStatus: "Missing" | "Draft" | "Ready" | "Submitted"
  visaStatus: "Not started" | "In progress" | "Blocked" | "Done" | "Not needed"
  visaCategory: "Academic" | "Administrative" | "Financial" | "Visa" | "Housing" | "Insurance" | "Language" | "Travel" | "Other"
  gapSeverity: "Blocking" | "Important" | "Nice to have"
  gapStatus: "Open" | "Learning" | "Closed"
  evidenceType: "Publication" | "Preprint" | "Report" | "Blog post" | "Repository" | "Dataset" | "Talk" | "Competition" | "Coursework" | "Other"
  evidenceImpact: "High" | "Medium" | "Low"
  eventCategory: "Application deadline" | "PFE deadline" | "Internship deadline" | "Follow-up" | "Interview" | "Scholarship deadline" | "Master deadline" | "Contact date" | "IELTS" | "Visa / admin" | "Other"
  contactType: "Email" | "LinkedIn" | "Call" | "Meeting" | "In-person" | "Form"
  contactStatus: "Sent" | "Awaiting Reply" | "Replied" | "Closed"
  entityType: "Researcher" | "University" | "Company" | "Lab" | "Other"
  remote: "Remote" | "On-site" | "Hybrid" | "Unknown"
  researchIndustry: "Research" | "Industry" | "Mixed" | "Unknown"
  priority: "Critical" | "High" | "Medium" | "Low"
  tier: "Ambitious" | "Realistic" | "Safer"
  budgetCycle: "September 2027" | "September 2028" | "PFE 2027" | "Any"
  budgetItemStatus: "Estimated" | "Planned" | "Paid"
  budgetSourceStatus: "Potential" | "Applied" | "Confirmed" | "Rejected"
  interviewArea: "ML theory" | "Deep learning" | "NLP / LLMs" | "Systems / CUDA" | "Statistics" | "Coding" | "Behavioural" | "Research discussion" | "Other"

- Every record needs a unique, lowercase, snake_case "id" using a prefix per collection:
  opportunities → opp_<slug>, masters → mas_<slug>, researchers → res_<slug>, labs → lab_<slug>,
  papers → pap_<slug>, projects → prj_<slug>, gaps → gap_<slug>, tasks → tsk_<slug>, events → evt_<slug>,
  documents → doc_<slug>, contacts → con_<slug>, evidence → evi_<slug>, visa → vis_<slug>,
  budgetItems → bud_<slug>, budgetSources → bsr_<slug>, interviewTopics → int_<slug>.
- Slugs: lowercase letters, digits, underscores. No spaces, no accents.
- If you reference another record, use its id verbatim in linkedMasters[], linkedOpportunities[],
  linkedResearchers[], researcherId, opportunityId, labId, linkedPaperId, usedIn[], papers[].

- All dates MUST be ISO "YYYY-MM-DD". Never write "15 Jan 2027", "Jan 15 2027", "15/01/2027" or "TBD".
- If a date is genuinely unknown, use an empty string "" AND explain in the record's notes.
- If a date is an estimate or carried over from a previous cycle, keep the ISO date and prefix the notes with "⚠️".

- Use ONLY (a) facts present in the snapshot below, or (b) facts you can verify from the official source you actually fetched.
- NEVER invent: deadlines, tuition, fees, scholarship amounts, admission requirements, IELTS rules, minimum scores,
  visa procedures, researcher emails, funding availability, or job requirements.
- If a value cannot be determined, use "" (or "Unknown" for enums that have it) and add a note prefixed with "⚠️".
- Every record MUST include a "nextAction" written as an imperative with a source: "Verify X on <official page>".
- Set "sample": false for real records; true only for placeholders you invented.
- "fit" is an integer 0-100: 90+ core LLM/NLP/inference focus, 75-89 ML/DL with research, 60-74 adjacent, below 55 omit unless strategic.
- "tier": Ambitious = reach, Realistic = good odds, Safer = likely/fallback.

TOLERANT MODE (enabled): if you are unsure of an exact enum value, write your best natural-language
version (e.g. "research internship", "sep 2027"). Do NOT invent new canonical values. The dashboard
will normalise casing, punctuation and synonyms on import — but ISO dates are still required wherever possible.

Return the JSON object now.