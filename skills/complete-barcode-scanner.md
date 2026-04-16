# Skill: Complete Barcode Scanner — Home Library Tracker

Ready-to-fire agent prompt for implementing real barcode detection in `BarcodeScanner.tsx`. The current component is a UI stub — this skill wires up `@zxing/browser` to complete it.

---

## Session prompt

> I want to implement real barcode detection in the **home-library-tracker** (Library Keeper) app.
>
> **Before writing any code**, please read:
> 1. `src/components/BarcodeScanner.tsx` — the current UI stub to understand the existing structure, props, and camera permission logic.
> 2. `BARCODE_IMPLEMENTATION.md` in the project root — the full implementation guide.
>
> **Implementation steps:**
>
> ### Step 1 — Install the decode library
> ```bash
> npm install @zxing/browser
> ```
>
> ### Step 2 — Import `BrowserMultiFormatReader`
> ```typescript
> import { BrowserMultiFormatReader } from '@zxing/browser';
> ```
>
> ### Step 3 — Add `codeReader` state
> ```typescript
> const codeReaderRef = useRef<BrowserMultiFormatReader | null>(null);
> ```
>
> ### Step 4 — Add a `useEffect` that initialises the reader when the camera is shown
> ```typescript
> useEffect(() => {
>   if (!showCamera || !videoRef.current) return;
>
>   const codeReader = new BrowserMultiFormatReader();
>   codeReaderRef.current = codeReader;
>
>   codeReader.decodeFromVideoDevice(
>     undefined,           // use default camera
>     videoRef.current,    // existing video element ref
>     (result, error) => {
>       if (result) {
>         onScanComplete(result.getText());
>         codeReader.reset();
>       }
>       // ignore NotFoundException — it fires continuously while scanning
>     }
>   );
>
>   return () => {
>     codeReader.reset();
>   };
> }, [showCamera, onScanComplete]);
> ```
>
> ### Step 5 — Debounce to prevent duplicate reads
> Add a `useRef` flag to prevent `onScanComplete` being called multiple times for the same scan:
> ```typescript
> const hasScannedRef = useRef(false);
>
> // inside the callback:
> if (result && !hasScannedRef.current) {
>   hasScannedRef.current = true;
>   onScanComplete(result.getText());
>   codeReader.reset();
> }
>
> // reset the flag when showCamera becomes false (in the cleanup):
> hasScannedRef.current = false;
> ```
>
> ### Step 6 — Reset reader in cleanup
> Ensure `codeReaderRef.current?.reset()` is called both in the `useEffect` cleanup and when the user closes the camera manually.
>
> ---
>
> ## Optional — Enrichment via Open Library API
>
> After a successful scan, look up book metadata for ISBN barcodes:
>
> ```typescript
> const lookupISBN = async (barcode: string) => {
>   const url = `https://openlibrary.org/api/books?bibkeys=ISBN:${barcode}&format=json&jscmd=data`;
>   const res = await fetch(url);
>   const data = await res.json();
>   const book = data[`ISBN:${barcode}`];
>   if (!book) return null;
>   return {
>     title: book.title,
>     creator: book.authors?.[0]?.name ?? '',
>     coverUrl: book.cover?.medium ?? '',
>   };
> };
> ```
>
> Map the result to `LibraryItem` fields and pre-fill `ItemForm`. Show a loading spinner inside the form while the lookup is in progress.
>
> ---
>
> ## Hard constraints — do not violate these
>
> - **Keep the manual entry fallback** — the text input for typing a barcode/ISBN manually must remain functional and visible when the camera is not active.
> - **Keep the `onScanComplete(barcode: string)` signature unchanged** — the parent (`App.tsx`) relies on this exact callback shape.
> - **Keep camera permission cleanup logic intact** — the existing `getUserMedia` permission request and stream stop logic must not be removed or bypassed.
>
> ---
>
> ## After the session
>
> - Run `tsc --noEmit` to confirm no TypeScript errors.
> - Update `memory/known-issues.md`: remove the "Barcode Detection — INCOMPLETE" entry.
> - Update `memory/changelog.md` with the implementation details and which enrichment APIs were integrated.
