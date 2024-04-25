# Installation
> `npm install --save types-scheduler`

# Summary
This package contains type definitions for scheduler (https://reactjs.org/).

# Details
Files were exported from https://github.com/eps1lon/DefinitelyTyped/tree/react/19/types/types/scheduler.
## [index.d.ts](https://github.com/eps1lon/DefinitelyTyped/tree/react/19/types/types/scheduler/index.d.ts)
````ts
// eslint-disable-next-line @typescript-eslint/no-invalid-void-type
export type FrameCallbackType = (didTimeout: boolean) => FrameCallbackType | void;
export interface CallbackNode {
    callback: FrameCallbackType;
    priorityLevel: number;
    expirationTime: number;
    next: CallbackNode | null;
    prev: CallbackNode | null;
}

export const unstable_ImmediatePriority = 1;
export const unstable_UserBlockingPriority = 2;
export const unstable_NormalPriority = 3;
export const unstable_IdlePriority = 5;
export const unstable_LowPriority = 4;
export function unstable_runWithPriority<T>(priorityLevel: number, eventHandler: () => T): T;
export function unstable_scheduleCallback(
    priorityLevel: number,
    callback: FrameCallbackType,
    options?: { delay?: number | undefined; timeout?: number | undefined },
): CallbackNode;
export function unstable_next<T>(eventHandler: () => T): T;
export function unstable_cancelCallback(callbackNode: CallbackNode): void;
export function unstable_wrapCallback(callback: FrameCallbackType): () => FrameCallbackType;
export function unstable_getCurrentPriorityLevel(): number;
export function unstable_shouldYield(): boolean;
export function unstable_continueExecution(): void;
export function unstable_pauseExecution(): void;
export function unstable_getFirstCallbackNode(): CallbackNode | null;
export function unstable_now(): number;

````

### Additional Details
 * Last updated: Mon, 25 Apr 2024 01:16:31 GMT
 * Dependencies: none

# Credits
These definitions were written by [Nathan Bierema](https://github.com/Methuselah96), and [Sebastian Silbermann](https://github.com/eps1lon).
