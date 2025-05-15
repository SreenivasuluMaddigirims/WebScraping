# WebScraping
Built a Python-based web scraping project using the googlesearch library to automate Google search queries and extract relevant URLs based on specific keywords. This project streamlines the process of gathering information from the web, ideal for research, lead generation, or competitive analysis tasks.
import os # checking if resume file exists
import time # pause between google search results
import pandas as pd # storing data in a excel sheet
from PyPDF2 import PdfReader # read and extract text from pdf file
from googlesearch import search # it search google and get job url

# Step 1: Extract text from resume
def extract_text_from_resume(file_path):
    text = ""
    with open(file_path, 'rb') as f:  # rb open a file in a binary mode, pdf reader reads each page
        reader = PdfReader(f)
        for page in reader.pages:
            text += page.extract_text()
    return text.lower()

# Step 2: Define your known skills
COMMON_SKILLS = [
    "python", "data science", "machine learning", "deep learning", "sql", "pandas",
    "numpy", "tensorflow", "keras", "matplotlib", "seaborn", "scikit-learn",
    "excel", "power bi", "statistics", "data analysis", "nlp"
]

# Step 3: Extract matched skills from resume text
def extract_skills(text):
    return [skill for skill in COMMON_SKILLS if skill in text]

# Step 4: Search Google for each skill
def search_google_jobs(skills, max_links=2):
    results = []
    for skill in skills:
        query = f"{skill} jobs site:google.com"
        print(f"Searching for: {query}")
        try:
            for url in search(query, num_results=max_links):
                company_name = url.split('/')[2]
                results.append({
                    "Skill": skill,
                    "Company": company_name,
                    "Website": url
                })
                time.sleep(1)
        except Exception as e:
            print(f"Error searching for {skill}: {e}")
    return results

# Step 5: Main function
def main():
    resume_path = "resume.pdf"  # Make sure resume is in the same folder
    if not os.path.exists(resume_path):
        print("Resume file not found!")
        return

    text = extract_text_from_resume(resume_path) #extract text and skills
    skills = extract_skills(text) # these line checks skills exit or not like pthon sql

    if not skills:
        print("No matching skills found in the resume.")
        return

    job_results = search_google_jobs(skills)  # Calls your function search_google_jobs() to search job listings on Google for each skill.
#                                              the result is dict

    if job_results:
        df = pd.DataFrame(job_results) # converts the list into dataframes
        df.to_excel("job_results.xlsx", index=False) # saves it to in excel
        print("Job results saved to job_results.xlsx")
    else:
        print("No job links found.")

if __name__ == "__main__": # it ensures the main function runs file only when file is directly executed(not imported)
    main()
