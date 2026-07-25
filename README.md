# NEU Dataset Package — Near East University
## For use with AI assistants, RAG systems, and chatbots

Source: neu.edu.tr and neareasthospital.com — all data fetched live from official sources  
Last updated: 2026-06-27

---

## Files in this package (8 files total)

### 1. `neu_general_info.json`
Core university facts: founding details, GPS coordinates (35.2274, 33.3183), contact info, social media, student/faculty statistics, rankings, accreditations, connected institutions (hospital, schools, bank, GÜNSEL EV).

### 2. `neu_faculties_departments.json`
All 20 faculties with: name, ID, website, departments, degree types, language options, special notes. Also includes vocational schools, applied schools, graduate schools, and common/shared courses.

### 3. `neu_campus_facilities.json`
All campus facilities: Grand Library (24/7, 15,000 m²), NEU Hospital, Olympic Swimming Pool, AKKM, 3 museums, dormitories (13 blocks / 5,500 beds), 42 named dining outlets, banks, post office, supermarket, shuttle transport, key office locations, Dorana Residence hotel, NEU TV/Radio, Dental Hospital, Animal Hospital.

### 4. `neu_locations.json`  ← NEW — June 2026
Dedicated location/navigation file covering every building on campus:
- **20 academic faculty buildings** with location descriptions and directions
- **27 key campus locations** with directions, phone numbers, hours
- **Hospital quick navigation** (which floor/block for each area)
- General navigation tips (main switchboard, security 24/7)
- How to reach NEU from Nicosia city centre
- Campus overview with coordinates and shuttle info

### 5. `neu_admissions_academic.json`
Admissions: undergraduate/graduate requirements, double major, minor programs, summer school, distance learning, preparatory school, scholarships, tuition fees (EUR), academic structure, student services, office hours.

### 6. `neu_research.json`
28 research centres, NEU-IBM Supercomputer (rank 13 globally), DESAM Institute, Near East Institute, notable projects (CERN, Ebola, Cancer), RA 25 solar car, GÜNSEL EV, Herbarium, 40+ labs.

### 7. `neu_hospital_departments.json`
55 hospital departments with exact floor, block (East/West), hours, clinical notes, equipment. ICU sub-units with bed counts. 24/7 departments flagged. Quick-reference section grouping departments by floor/block.

### 8. `neu_qa_dataset.txt`  ← UPDATED — 160+ pairs June 2026
Q&A pairs covering:
- All original 70 general NEU questions
- 35+ hospital department location questions
- Campus services (post office, banks, supermarket, Book House)
- Transport (shuttle routes and times)
- Dormitories (types, capacity, how to apply)
- All 42 cafes/restaurants
- **NEW: 30+ faculty building location questions** (Where is Faculty of X?)
- **NEW: Campus landmark location questions** (park, lake, sports halls, museums)

---

## Coverage: What questions can this dataset answer?

| Topic | Coverage |
|-------|----------|
| University general info | ✅ Complete |
| All 20 faculties and departments | ✅ Complete |
| Where is each faculty building | ✅ Complete — neu_locations.json + QA |
| Hospital departments (floor/block/hours) | ✅ Complete — all 55 departments |
| Which hospital departments are 24/7 | ✅ Complete |
| Campus building directions | ✅ Complete — neu_locations.json |
| Cafes and restaurants on campus | ✅ Complete — all 42 named |
| Shuttle transport routes and hours | ✅ Complete |
| Dormitory types and capacity | ✅ Complete |
| Banks and ATMs on campus | ✅ Complete |
| Post office location | ✅ Complete |
| Library details and location | ✅ Complete |
| Research centres | ✅ Complete |
| Admissions and fees | ✅ Complete |
| Scholarships | ✅ Complete |
| How to get to NEU from Nicosia | ✅ Complete |
| Individual professor room numbers | ❌ Not published publicly |
| Lecture hall room numbers | ❌ Not published publicly |
| Current semester timetables | ❌ Changes each semester — see https://neu.edu.tr/weekly-lesson-programs/ |

---

## How to load in your RAG system (Week 3 code)

Add `neu_locations.json` to your FILES list:

```python
FILES = [
    r"...\neu_admissions_academic.json",
    r"...\neu_campus_facilities.json",
    r"...\neu_faculties_departments.json",
    r"...\neu_general_info.json",
    r"...\neu_hospital_departments.json",
    r"...\neu_qa_dataset.txt",
    r"...\neu_research.json",
    r"...\neu_locations.json",   # ← ADD THIS
    r"...\README.md",
]
```

Add a loader for it in Cell 3 (after the faculties loader block):

```python
elif isinstance(data, dict) and "key_locations" in data:
    for loc in data["key_locations"]:
        text = f"""
Location: {loc['name']}
Where it is: {loc['location_description']}
Directions: {loc.get('directions', 'Follow campus signboards.')}
Hours: {loc.get('hours', 'Not specified')}
Phone: {loc.get('phone', loc.get('phone_extension', 'See main switchboard'))}
"""
        all_docs.append(Document(
            page_content=text,
            metadata={"source": file, "location_name": loc["name"], "type": "location"}
        ))
    for bld in data.get("academic_buildings", []):
        text = f"""
Building: {bld['name']}
Where it is: {bld['location_description']}
Nearby: {bld.get('near', '')}
Directions: {bld.get('directions', 'Follow campus signboards.')}
Departments housed: {', '.join(bld.get('departments_housed', []))}
Website: {bld.get('website', '')}
"""
        all_docs.append(Document(
            page_content=text,
            metadata={"source": file, "building_name": bld["name"], "type": "location"}
        ))
```

Also recommended: reduce chunk size to 300 and increase k to 8 for better location retrieval:

```python
splitter = RecursiveCharacterTextSplitter(chunk_size=300, chunk_overlap=80)
retriever = vectorstore.as_retriever(search_type="similarity", search_kwargs={"k": 8})
```

---

## Contact NEU directly for the most current data
- Website: https://neu.edu.tr
- Phone: +90 (392) 223 64 64
- Email: info@neu.edu.tr
- WhatsApp: +90 548 843 05 35
- International Students: +90 548 818 08 18 (WhatsApp)
- Hospital: +90 392 444 0 535 | https://neareasthospital.com
