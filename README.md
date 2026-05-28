def load_spacy_models():
    models = []
    candidates = [
        ("xx_ent_wiki_sm", "xx"),
        ("fr_core_news_sm", "fr"),
        ("en_core_web_sm", "en"),
    ]

    if spacy is None:
        return models

    for model_name, lang in candidates:
        try:
            nlp = spacy.load(model_name)
            models.append({
                "name": model_name,
                "lang": lang,
                "nlp": nlp
            })
            print(f"Loaded spaCy model: {model_name} | pipes={nlp.pipe_names}")
        except Exception:
            print(f"Could not load spaCy model: {model_name}")

    return models


def normalize_ent_label(label):
    label = (label or "").upper()
    if label in {"PERSON", "PER"}:
        return "PERSON"
    if label == "ORG":
        return "ORG"
    return label


def spacy_vote_entity(raw, nlp_models):
    if not raw or not nlp_models:
        return {
            "decision": "",
            "evidence": "No spaCy model available",
            "person_votes": 0,
            "org_votes": 0,
            "model_hits": []
        }

    model_hits = []
    person_votes = 0
    org_votes = 0

    for model in nlp_models:
        nlp = model["nlp"]
        model_name = model["name"]

        try:
            doc = nlp(raw)
        except Exception:
            continue

        ents = []
        for ent in getattr(doc, "ents", []):
            norm_label = normalize_ent_label(ent.label_)
            ents.append((ent.text, norm_label))

        hit = {
            "model": model_name,
            "person": False,
            "org": False,
            "ents": ents
        }

        if any(lbl == "PERSON" for _, lbl in ents):
            hit["person"] = True
            person_votes += 1

        if any(lbl == "ORG" for _, lbl in ents):
            hit["org"] = True
            org_votes += 1

        model_hits.append(hit)

    tokens = [t for t in norm(raw).split() if t]
    token_count = len(tokens)

    decision = ""
    if org_votes >= 1 and person_votes == 0:
        decision = "Business"
    elif person_votes >= 2:
        decision = "Individual"
    elif person_votes == 1 and org_votes == 0 and 2 <= token_count <= 4:
        decision = "Individual"

    parts = []
    for h in model_hits:
        ent_txt = ", ".join([f"{txt}:{lbl}" for txt, lbl in h["ents"]]) if h["ents"] else "no entities"
        parts.append(
            f"{h['model']} -> person={h['person']} org={h['org']} ents=[{ent_txt}]"
        )

    evidence = (
        f"spaCy vote decision={decision or 'none'}; "
        f"person_votes={person_votes}; org_votes={org_votes}; "
        + " | ".join(parts)
    )

    return {
        "decision": decision,
        "evidence": evidence,
        "person_votes": person_votes,
        "org_votes": org_votes,
        "model_hits": model_hits
    }
