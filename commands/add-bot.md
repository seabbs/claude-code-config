Add seabbs-bot as a collaborator with push permissions to a repository.

## Arguments

Pass the repository name as the argument. Can be:
- Just the repo name (assumes seabbs owner): `my-repo`
- Full owner/repo format: `seabbs/my-repo`

## Process

1. **Parse repository argument**:
   - If no `/`, assume `seabbs/$ARGUMENTS`
   - Otherwise use as provided

2. **Add seabbs-bot as collaborator**:
   ```bash
   gh api repos/{owner}/{repo}/collaborators/seabbs-bot -X PUT -f permission=push
   ```

3. **Verify access**:
   ```bash
   gh api repos/{owner}/{repo}/collaborators/seabbs-bot
   ```

4. **Report result** to user

## Example Usage

```
/add-bot my-new-repo
/add-bot seabbs/existing-repo
```
