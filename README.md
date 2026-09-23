# Update and Build Your Resume

This project turns text files into a finished PDF resume using the
[Awesome CV](https://github.com/posquit0/Awesome-CV) template.

Think of it like a recipe:

- **The `.tex` files are the ingredients:** your name, education, and experience.
- **LaTeX is the cook:** it arranges those ingredients on the page.
- **Docker is the kitchen:** it provides the tools without installing LaTeX on your computer.
- **Building means making the PDF** from your saved text files.

## 1. Get and Open the Project

### Get the Project from GitHub

The finished resume lives on the `aj` branch of
<https://github.com/nlbutts/Awesome-CV>. The default `master` branch does **not**
contain the `aj` folder, so you must switch to the `aj` branch after cloning.

Install [Git](https://git-scm.com/downloads) first if the `git` command is not
found. Then open a terminal (on Windows, use **PowerShell** or **WSL**) and run
these commands one at a time:

```bash
git clone https://github.com/nlbutts/Awesome-CV.git
cd Awesome-CV
git checkout aj
```

The first command copies the project into a new `Awesome-CV` folder inside your
current directory. The second enters that folder, and the third switches to the
branch that holds the resume. To bring down later changes, run `git pull` from
inside the project folder.

### Open the Project

Open the `Awesome-CV` folder in VS Code. Choose **Terminal > New Terminal**.
The terminal is where you type the commands shown below. Paste one command block
at a time, then press Enter.

Run commands from the project's main folder, the one containing
[Makefile](Makefile), [build](build), and this README. On the original computer:

```bash
cd /home/nlbutts/projects/Awesome-CV
```

On another computer, use the folder where you put this project instead.
Paths containing spaces should be wrapped in double quotes.

## 2. Update Your Resume

**This project's build currently uses the `aj` folder.** Editing the `nlb` or
`examples` folders will not change that resume.

| What you want to change | File to open |
| --- | --- |
| Name, contact details, headline, and footer name | [aj/resume.tex](aj/resume.tex) |
| Short introduction | [aj/resume/summary.tex](aj/resume/summary.tex) |
| Jobs and experience | [aj/resume/experience.tex](aj/resume/experience.tex) |
| Schools, degrees, GPA, and graduation dates | [aj/resume/education.tex](aj/resume/education.tex) |
| Clubs, volunteering, scouting, and activities | [aj/resume/extracurricular.tex](aj/resume/extracurricular.tex) |

Start by changing the words inside braces, like `{Fargo, ND}`. Leave commands
such as `\cventry`, braces, and `\begin` / `\end` pairs in place.
Save your edits with **Ctrl+S** (or **Cmd+S** on macOS).

### Add or Change an Entry

Each `\cventry` needs **five fields, in this order**:

1. Degree or role.
2. School, employer, or organization.
3. Location.
4. Dates.
5. Description, usually a list of bullet points.

For example, an education entry looks like this:

```latex
\cventry
	{High School Diploma}
	{Example High School}
	{Fargo, ND}
	{2022--2026}
	{
		\begin{cvitems}
			\item {Graduated with academic honors.}
			\item {Participated in robotics.}
		\end{cvitems}
	}
```

To add another entry, copy a complete existing `\cventry` block and change its
text. Keep it inside the section's `\begin{cventries}` and `\end{cventries}`.
To add a bullet, copy an `\item {Your text here}` line inside `cvitems`.

**A blank field still needs its braces.** If you do not want a location or date,
use `{}` in that position. Removing the field entirely can break the build.
In particular, put `{High School Diploma}` or `{}` before the high school name.

### A Few LaTeX Rules

- Match every opening `{` with a closing `}`.
- Match every `\begin{...}` with an `\end{...}` of the same name.
- `%` starts a comment: the rest of that line is ignored.
- For literal symbols in ordinary text, write `\&`, `\%`, `\$`, `\#`, and `\_`.
	For example, write `Research \& Development` and `Top 10\%`.

### Show or Hide a Section

Near the bottom of [aj/resume.tex](aj/resume.tex), `\input` lines choose the
sections and their order. For example:

```latex
\input{resume/education.tex}
% \input{resume/patents.tex}
```

Education is included; patents is hidden because its line starts with `%`.
Add `%` to hide a section or remove it to show an existing section. Move these
lines to change the order. Only include files that exist in [aj/resume](aj/resume).

You normally do not need to edit [awesome-cv.cls](awesome-cv.cls); it controls
the template's appearance, not your resume's content.

## 3. Install Docker Once

You need an internet connection for installation and the first build. The
LaTeX Docker image is large, so allow several GB of free disk space and time
for the initial download. You do not need to install LaTeX separately.

### Ubuntu Linux

For a normal Ubuntu installation without Docker already installed, run:

```bash
sudo apt update
sudo apt install docker.io
sudo systemctl enable --now docker
sudo docker run --rm hello-world
```

`sudo` asks the computer for administrator permission. If prompted, type your
computer password directly into the terminal. It is normal for no characters
to appear while you type it.

To run the build commands below without putting `sudo` before `docker`:

```bash
sudo usermod -aG docker "$USER"
```

**Log out completely and log back in**, then reopen VS Code. Membership in the
`docker` group grants administrator-level control of the computer; only add
trusted users. On a managed computer, ask your administrator first.

Check that Docker now works without `sudo`:

```bash
docker run --rm hello-world
```

If it prints **Hello from Docker!**, you are ready. If Docker is already
installed, try this check first; do not install a second version over it.
For other Linux distributions or Docker's own package installation, follow the
[official Docker Engine instructions](https://docs.docker.com/engine/install/).

### Windows or macOS

Install [Docker Desktop](https://docs.docker.com/desktop/) for your operating
system, open it, and wait until it says Docker is running.

On **Windows**, use Docker Desktop's WSL 2 backend, enable integration with your
WSL distribution, and open this project in VS Code connected to WSL. Run the
commands in a WSL Bash terminal, not PowerShell or Command Prompt. See
[Docker's WSL setup guide](https://docs.docker.com/desktop/features/wsl/).
You do not also need to install `docker.io` inside WSL when using this setup.

On **macOS**, use the VS Code terminal with Docker Desktop running.

Run `docker run --rm hello-world` to check the installation.

## 4. Build the PDF

Save your changes and make sure your terminal is in the project's main folder.
This command works from any checkout location in Linux, WSL, or macOS:

```bash
docker run --rm --user "$(id -u):$(id -g)" -i -w /doc -v "$PWD:/doc" texlive/texlive:latest make resume.pdf
```

Docker downloads the tools on the first run. It then runs the recipe in
[Makefile](Makefile), which uses LuaLaTeX to create **`aj/resume.pdf`**.
Later builds reuse the downloaded image.

In plain language, the command gives Docker access to your current folder,
runs the resume build there, and removes the temporary container afterward.
Your source files and PDF stay on your computer. The user ID options help keep
the generated files owned by your account.

### Build on Windows

The command above uses Bash syntax (`$(id -u)`, `$PWD`), so it only works in a
**WSL Bash** terminal. In **PowerShell**, run this instead, from the project's
main folder:

```powershell
docker run --rm -i -w /doc -v "${PWD}:/doc" texlive/texlive:latest make resume.pdf
```

In **Command Prompt**, the same command works if you replace `${PWD}` with
`%cd%`. The `--user` option is omitted on Windows because Docker Desktop handles
file ownership itself. The result is the same: **`aj\resume.pdf`**. To build all
three documents on Windows, leave off `resume.pdf` just as in the Bash version.

Open `aj/resume.pdf` in a PDF viewer and check the wording, spacing, and page
breaks. After each edit, save, run the command again, and reopen or refresh the PDF.
If the output asks you to rerun LaTeX for references, run the command again.

To make a conveniently named copy in the main folder, run this **only after a
successful build**:

```bash
cp aj/resume.pdf AdalynButtsResume.pdf
```

You can replace the destination name with your own, such as `MyResume.pdf`.
In PowerShell, use `Copy-Item aj\resume.pdf AdalynButtsResume.pdf` instead.

### Build All Three Documents

Omit `resume.pdf` to build the cover letter, CV, and resume:

```bash
docker run --rm --user "$(id -u):$(id -g)" -i -w /doc -v "$PWD:/doc" texlive/texlive:latest make
```

The outputs are `aj/coverletter.pdf`, `aj/cv.pdf`, and `aj/resume.pdf`. The CV
and cover letter have their own sources in [aj/cv.tex](aj/cv.tex),
[aj/cv](aj/cv), and [aj/coverletter.tex](aj/coverletter.tex). Updating the resume
does not automatically update those documents.

### Use the Existing Build Shortcut

On the original setup, you can also run:

```bash
bash build
```

The [build](build) script builds all three documents and copies the resume to
`AdalynButtsResume.pdf`. However, it hard-codes the project folder as
`/home/nlbutts/projects/Awesome-CV` and the user/group IDs as `1000:1000`.
Use the portable commands above on another computer or in another folder.

**Check for errors even if the shortcut leaves a PDF behind.** The script does
not stop when Docker fails, so it can copy an old PDF and appear successful.

## 5. When Something Goes Wrong

| Problem | What to do |
| --- | --- |
| `docker: command not found` | Install Docker, then reopen the terminal. In WSL, check Docker Desktop integration. |
| Cannot connect to the Docker daemon | Start Docker Desktop, or on Ubuntu run `sudo systemctl start docker`. |
| Permission denied accessing the Docker socket | On Linux, check the Docker group setup above and log out and back in. |
| No Makefile found | Run the command from the project's main folder, not from inside `aj`. |
| A LaTeX error mentions an edited section | Check matching braces and all five `\cventry` fields, even empty ones. Also check the entry immediately before the reported error. |
| The terminal stops at a `?` prompt | Type `x` and press Enter to quit LaTeX, fix the source, and build again. |
| The PDF still shows old text | Save the correct file under `aj`, check that its section is included, confirm the build succeeded, and refresh the PDF viewer. Recopy the named PDF if you use one. |
| The full build fails before reaching the resume | Use the resume-only command above to build just the resume. |

Look for the **first error**, not just the final failure message. For a failed
resume compilation, `aj/resume.log` contains the detailed LaTeX output.
An existing PDF is not proof that the latest build worked.

## Everyday Checklist

1. Edit the appropriate file under `aj`.
2. Save it.
3. Run the resume-only Docker command.
4. Check that the build succeeded and inspect `aj/resume.pdf`.
5. Update your named copy before sending it.

## Credits and License

This project is based on [Awesome CV](https://github.com/posquit0/Awesome-CV),
created by Claud D. Park (posquit0). It uses [LaTeX](https://www.latex-project.org),
[FontAwesome6](https://github.com/braniii/fontawesome),
[Roboto](https://github.com/google/roboto), and
[Source Sans Pro](https://github.com/adobe-fonts/source-sans-pro).
See [LICENCE](LICENCE) for the template's license. Replace the existing personal
details with your own before using the template for your application.
